---
title: Nginx rtmp 模块配合 OBS 推流实现直播(监控)功能
date: 2017/12/11
categories:
  - 1-网络笔记
tags:
  - 环境部署
  - Nginx
copyright: true
---

写的有点仓促，有问题的话可以随时留言。

客户端环境：
系统：`WIN 10`
推流软件：`OBS Studio`
PC测试浏览器：`Chrome`
`Android` 测试浏览器：`QQ` 内置

服务器环境：
系统：`Centos 7`
代理：`Nginx（ http+rtmp )`
浏览器播放器：`ckplayer`

## 1. 安装 `Nginx`

具体过程参考 [Centos 7 下配置 Nginx…][1]

## 2. 安装编译 rtmp 模块

```powershell
# 下载 rtmp 模块压缩包(最好和 Nginx 的安装包在同一目录下)
wget https://github.com/arut/nginx-rtmp-module/archive/master.zip
# 解压
unzip nginx-rtmp-module-master.zip
# 得到 nginx-rtmp-module-master
# 进入 Nginx 安装包文件夹
cd nginx-1.12.2
# 编译安装 rtmp
./configure --add-module=/root/nginx-rtmp-module && make && make install
# 注意将 /root/nginx-rtmp-module 替换成你 rtmp 包所在的位置
```

## 3. 配置 Nginx 的 rtmp 和 http 模块

在 `Nginx` 配置目录（一般为：`usr/local/nginx/conf`）下编辑 `live_rtmp.conf` 文件如下：

```config
#user  nobody;
worker_processes  1;

error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

rtmp {

    server {
        listen 1935;
        chunk_size 4000;
        application mylive {
            live on;
            record all;
            record_path /home/live_record;
            record_max_size 200M;
            hls on;
			# 注意此地址设置，hls_path 代表 hls 目录及.m3u8文件的保存目录
            hls_path /var/www/html/hls;
            hls_fragment 1s;
            hls_playlist_length 5;
            allow play all;
        }
    }
}

http {
    server {
        listen       8080;
        location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl {
            # Copy stat.xsl wherever you want
            # and put the full directory path here
            root /usr/local/nginx/html/;
        }

        location /hls {
            # Serve HLS fragments
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            root /var/www/html/hls;
            add_header Cache-Control no-cache;
        }
		# http 根目录
		location / {
			root /var/www/html;
		}
   }
}
```

### 验证配置文件正确性：

```powershell
/usr/local/nginx/sbin/nginx -t  -c /usr/local/nginx/conf/live_rtmp.conf
```

### 配置文件说明：

1. `hls` 开启后产生的 `m3u8` 文件会存在 `hls_path` 下，播放端调用 `404` 的时候先去看看 `m3u8` 有没有生成。
2. `stat.xsl` 要从 `nginx-rtmp` 的源代码根目录中考出来，考到配置的那个文件夹。

## 4. 启动 Nginx

```powershell
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/live_rtmp.conf
```

推流地址： `rtmp://服务器ip/mylive/xxxxx`
播放地址： `rtmp://服务器ip/mylive/xxxxx`
`hls` 地址： `http://服务器ip:8080/hls/xxxxx.m3u8`
状态查看地址： `http://服务器ip:8080/stat`
`xxxx` 代表你在 `OBS` 设置的流名称

## 5. 客户端下载并配置 OBS

下载地址：[OBS官网][2]

![OBS 配置][3]

## 6. 下载配置 ckplayer rtmp 浏览器播放器

下载地址：[clpalyer 官网][4]

下载后找到最里层的 `ckplayer` 文件夹通过 `FTP` 上传到服务器(或直接 `wget` 下载) `http` 根目录下。

![上传 ckplayer][5]

按照 [官网示例][6] 编辑一个 `index.html` 文件放在刚刚上传的 `ckplayer` 目录下。

![index.html][7]

## 7. 开启 OBS 推流访问网页查看直播(监控)

访问 `http://服务器ip:8080/ckplayer/index.html`
即可查看推流情况（贴图看疗效）
理论上服务器带宽越高，推流的 `FPS` 越小你的直播越流畅

![成果][8]

[1]: https://wiki.blanc.site
[2]: https://obsproject.com/
[3]: https://img.blanc.site/wiki/img/25.png
[4]: http://www.ckplayer.com/
[5]: https://img.blanc.site/wiki/img/23.png
[6]: http://www.ckplayer.com/sampleX/rtmp.html
[7]: https://img.blanc.site/wiki/img/24.png
[8]: https://img.blanc.site/wiki/img/26.png