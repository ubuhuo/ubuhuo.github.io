---
title: WordPress 配置七牛云 CDN 具体操作
date: 2017/11/29
categories:
  - 编程框架
  - WordPress
tags:
  - WordPress 配置七牛云 CDN
copyright: true
abbrlink: 6ec97606
---

## 1. 前言

前段时间突发奇想把原先挂在 `Coding` 上的 `Hexo` 博客迁到 `WordPress` 上了，反正阿里云送的服务器闲着也是闲着，于是就搭了一个 `WordPress` 博客，这个过程中遇到了好多坑，有的坑甚至折磨了我一个多星期，本着先照顾自己再关心大众的理念，我打算在我解决这些坑之后留下排坑博文，一来万一以后健忘的我又遇到了类似问题可以有所参考，二来也为之后遇到同类坑的朋友们提供排坑方法，话不多说，本篇博文将具体的讲解 `WordPress` 配置七牛云 `CDN` 的具体操作。

注：默认读者懂得基本的 `FTP`、`WordPress` 后台管理、域名解析等基本操作。

## 2. 前期准备

1. 认证了的七牛云账号一枚
2. 国内已经备案完成的域名一枚
3. 已经搭建好的 `WordPress` 博客平台一个
4. 带上脑子我们出发了……

## 3. 七牛云端设置流程

在为 `WordPress` 配置 `CDN` 之前我们先需要在七牛云端进行储存空间的基本设置：

### 3.1 新建储存空间

![新建储存空间][1]

### 3.2 绑定域名

![绑定域名][2]

![绑定域名][3]

### 3.3 域名解析

![域名解析][4]

![域名解析][5]

### 3.4 等待添加成功提示

![等待添加成功提示][6]

### 3.5 设置镜像源

![设置镜像源][7]

### 3.6 七牛云端设置完毕，跳转到 WordPress 后台

## 4. WordPress 后台设置流程

### 4.1 安装插件

搜索并安装图示两个插件，根据需求启动 `WPJAM BASIC` 插件中你需要的功能，不要忘记开启七牛云镜像云储存，下图为我的配置目录：

![插件下载][8]

![插件下载][9]

### 4.2 配置插件

![配置插件][10]

![配置插件][11]

![配置插件][12]

### 4.3 等待

设置后请等待约 30分钟 后刷新博客，查看网页源码即可发现，网站中的 `JS`、`CSS` 和图片的 `URL` 都转到你设置的 `CDN` 上了，如图：

![效果展示][13]

### 4.4 设置成功

[1]: https://img.blanc.site/wiki/img/74.png
[2]: https://img.blanc.site/wiki/img/75.png
[3]: https://img.blanc.site/wiki/img/76.png
[4]: https://img.blanc.site/wiki/img/77.png
[5]: https://img.blanc.site/wiki/img/78.png
[6]: https://img.blanc.site/wiki/img/79.png
[7]: https://img.blanc.site/wiki/img/80.png
[8]: https://img.blanc.site/wiki/img/81.png
[9]: https://img.blanc.site/wiki/img/82.png
[10]: https://img.blanc.site/wiki/img/83.png
[11]: https://img.blanc.site/wiki/img/84.png
[12]: https://img.blanc.site/wiki/img/85.png
[13]: https://img.blanc.site/wiki/img/86.png