---
title: ThinkPHP5 的 Nginx 配置
date: 2018/3/5
categories:
  - 1-网络笔记
  - ThinkPHP
tags:
  - ThinkPHP
copyright: true
---

## 1. 为什么要配置 Nginx？

因为 `ThinkPHP5` 采用了路由系统，而默认配置的 `Nginx` 无法识别 `ThinkPHP5` 产生的 `URL` 导致 `404`，另外有时候我们需要隐藏 `ThinkPHP` 的入口文件；

## 2. 主要修改 Nginx 配置文件的哪几个点？

我们主要修改配置文件中 `server {}` 中的三处地方：

1. 首先在 `server_name` 下设置 `$root` 变量：`set $root /app/www/mydomain.vm/public;`;
2. 然后再 `location / {}` 中修改 `root` 并添加正则匹配规则;
3. 最后设置 `location ~ .+\.php($|/) {}` 使网站解析 `PHP` 文件并识别 `ThinkPHP5` 路由产生的 `URL` ；

## 3. 具体配置文件如下：

在配置中我们默认：

1. 配置文件使用 `vhost` 方式，如果单主机的酌情修改；
2. 假定配置的域名是 `mydomain.vm`；
3. 假定用户使用的是 `Linux` 操作系统；
4. 假定项目文件部署在 `/app/www/mydomain.vm` 目录下；
5. 假定 `Nginx` 的日志放在 `/app/logs/nginx` 目录下。

```config
server {
    listen 80;
    server_name    mydomain.vm    www.mydomain.vm;      
    # 设置服务器名称[推荐保持默认设置]
    access_log    /app/logs/nginx/mydomain_access.log;  
    # 设置Nginx运行日志位置[推荐保持默认设置]
    error_log    /app/logs/nginx/mydomain_error.log;    
    # 设置Nginx错误日志位置[推荐保持默认设置]
    set        $root    /app/www/mydomain.vm/public;    
    # 设置Nginx解析根目录[即网站根目录，这里一般设置为ThinkPHP5的入口文件目录]
    location ~ .*\.(gif|jpg|jpeg|bmp|png|ico|txt|js|css)$
    {
        root $root;
    }
    location / {
        root    $root;
        index    index.html index.php;
        if ( -f $request_filename) {                     
        # 设置正则匹配规则隐藏入口文件
            break;
        }
        if ( !-e $request_filename) {
            rewrite ^(.*)$ /index.php/$1 last;
            break;
        }
    }
    location ~ .+\.php($|/) { 
	    # 这个地方一定注意，我们修改了原先的php文件匹配规则
        set $script $uri;    
        # 以下为设置 Ngxin 识别 ThinkPHP5 路由产生的 url
        set $path_info "";
        if ($uri ~ "^(.+\.php)(/.+)") {
            set $script $1;
            set $path_info $2;
        }
        fastcgi_pass     127.0.0.1:9000;  
        # 设置为 php-fpm 运行端口，让 php-fpm 解析 php 文件
        fastcgi_index    index.php?IF_REWRITE=1;  
        # 下面这些设置具体是做什么的我也不太清楚不过为必须设置不可省略
        fastcgi_param    PATH_INFO    $path_info;
        fastcgi_param    SCRIPT_FILENAME    $root$fastcgi_script_name;
        fastcgi_param    SCRIPT_NAME    $script;
        include        fastcgi_params;
    }
}
```

修改完配置文件之后记得重启一下 `Nginx`。