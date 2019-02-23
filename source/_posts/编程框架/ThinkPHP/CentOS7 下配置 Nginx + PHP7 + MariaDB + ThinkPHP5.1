---
title: CentOS7 下配置 Nginx + PHP7 + MariaDB + ThinkPHP5.1
date: 2018/2/21
categories: 
  - 编程框架
  - ThinkPHP
tags: 
  - ThinkPHP5.1 部署教程
copyright: true
---

最近突然想学习一下使用一些比较成熟的后台框架，考虑到之前帮大佬打下手的时候用过 `ThinkPHP` 所以就暂定了以 `ThinkPHP` 为主要学习目标。

下面是我在服务器端配置 `ThinkPHP` 所需环境而踩的一些坑（且有很多已经是老坑了）：

1. 本次配置选择的服务器系统为 `Centos7` 环境是 `Nginx + PHP7 + MariaDB + ThinkPHP5.1`
2. 我会按照我个人认为合适的顺序分别介绍他们的安装与配置过程

## 1. MariaDB 的安装与初始配置

```powershell
# yum 安装
yum -y install mariadb mariadb-server

# 安装完成MariaDB，首先启动MariaDB
systemctl start mariadb
# 设置开机启动
systemctl enable mariadb

# 接下来进行MariaDB的相关简单配置
mysql_secure_installation

#首先是设置密码，会提示先输入密码
Enter current password for root (enter for none):# 初次运行直接回车

#设置密码
Set root password? [Y/n] # 是否设置root用户密码，输入y并回车或直接回车
New password: # 设置root用户的密码
Re-enter new password: # 再输入一次你设置的密码

#其他配置
Remove anonymous users? [Y/n] # 是否删除匿名用户
Disallow root login remotely? [Y/n] #是否禁止root远程登录
Remove test database and access to it? [Y/n] # 是否删除test数据库
Reload privilege tables now? [Y/n] # 是否重新加载权限表

#初始化MariaDB完成，接下来测试登录
mysql -uroot -ppassword
```

## 2. PHP7 + PHP-FPM 安装

```powershell
# 在 CentOS 7 通过 yum 安装 PHP 7，首先在终端运行：
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

# 提示错误：
error: Failed dependencies:
epel-release >= 7 is needed by webtatic-release-7-3.noarch

# 需要先安装 epel-release。
yum -y install epel-release 

# 再安装 PHP 7
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm # 获取 PHP 7 的 yum 源
yum install php70w php70w-fpm

# 验证安装
PHP -v，

# 显示当前 PHP 版本，信息如下：
[root@Ryoma /]# php -v
PHP 7.0.18 (cli) (built: Apr 15 2017 07:09:11) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies

# 这样就在 CentOS 7 下通过 yum 安装成功 PHP 7.
```

## 3. 编译安装 Nginx 并配置 PHP

```powershell
# [必须]安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：
yum install gcc-c++

# [必须]PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：
yum install -y pcre pcre-devel

# [自选]zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。
yum install -y zlib zlib-devel

# [自选]nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。
yum install -y openssl openssl-devel

# 官网下载 Ngxin
# 直接下载.tar.gz安装包，地址：https://nginx.org/en/download.html
# 推荐选择稳定版下载 ： 当前版本 1.12.2
# 下载链接： https://nginx.org/download/nginx-1.12.2.tar.gz
wget -c https://nginx.org/download/nginx-1.12.2.tar.gz

# 解压 用到了之前下载的zlib库 若没下载可选择其他方式解压
tar -zxvf nginx-1.12.2.tar.gz

# 编译前初始化配置
cd nginx-1.12.2 # 进入nginx目录
./configure # 使用默认配置

# 编译安装
make
make install

# 查找安装路径
[root@Ryoma nginx-1.12.2]#whereis nginx
nginx: /usr/local/nginx

# 启动、停止 nginx
cd /usr/local/nginx/sbin/ # 进入 nginx sbin 目录
./nginx # 启动 nginx
./nginx -s quit # 此方式停止步骤是待 nginx 进程处理任务完毕进行停止
./nginx -s stop # 此方式相当于先查出 nginx 进程id再使用kill命令强制杀掉进程
./nginx -s reload # 重新加载

#查询 nginx 进程：
ps aux|grep nginx

# 配置 nginx
vim /usr/local/nginx/conf/nginx.conf
# 个人配置图如下
```

![个人配置图][1]

## 4. 安装 ThinkPHP5

```powershell
# 全局安装 composer
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer

# tp5 5.1 版本安装
composer create-project topthink/think  tp5  --prefer-dist
# PHP 版本低于 5.6 的话 将仍然安装 5.0 版本
```

[1]: https://img.blanc.site/wiki/img/13.png