---
title: Windows 下 ThinkPHP5 配置 redis 缓存
date: 2018/5/15
categories:
  - 编程框架
  - ThinkPHP
tags:
  - ThinkPHP5 配置 redis 缓存
copyright: true
---

最近再写一个小的学校论坛项目，项目是基于 `Vue + ThinkPHP5` 开发的，在开发过程中遇到一个小问题让我纠结了好久，就是如何实现用户给帖子点赞。

用户给帖子点赞是一个很细节的小功能，在刚开始学习 `Web编程` 的时候曾经尝试过实现这个功能但总是会莫名其妙的卡住，当时觉得不是问题太难应该是我还没有学习相关的知识，直到最近开发项目的时候才发现问题并没有那么简单，我已经掌握了基本的前后端和数据库的知识但是具体实践起来还是有些困难，于是就百度了一下在 [V2EX][1] 上找到了这样一篇帖子 [小菜我准备写一个简单的论坛…][2]，看了里面大佬的回复才发现原来还是知识体系不全面，如大佬所言要很好的实现点赞功能需要借助 `redis` 等缓存机制来完成快速的读写。

于是我又赶紧补习了一下关于 `redis` 的一下皮毛并进行了 `Windows` 下 `ThinkPHP5` 配置 `redis` 缓存，过程中还是有几个小坑的，于是写下这篇文章记录一下：

## 1. 声明

本教程所配置的仅是 `Windows` 下的开发环境并非服务器环境若需要服务器环境配置请自行百度。

## 2. 安装 php 的 redis 和 igbinary 拓展

`php_igbinary`: 在序列化和反序列化的效率上高于其自带的
`php_redis`: 效率是相当高有链表排序功能

首先在 `phpinfo` 页面查看本机 `php` 版本

![phpinfo 信息][3]

然后分别点击 [redis 拓展下载地址][4] 和 [igbinary 拓展下载地址][5] 下载相应的拓展

下载完成后解压，将 `php_redis.dll` 和 `php_igbinary.dll` 拷贝至 `php` 的 `ext` 目录下，修改 `php.ini`，在该文件中加入

```ini
; php_redis  
extension=php_igbinary.dll  
extension=php_redis.dll
```

## 3. 下载 redis 的 Windows server

[下载地址][6]

下载，解压后得到文件夹重命名为 `redis` 并将得到的文件夹移动到任意你想放置的位置
打开一个 `cmd` 窗口 使用 `cd` 命令切换目录到 `redis` 目录下运行 `redis-server.exe redis.windows.conf`

![redis运行][7]

得到上图信息则说明安装成功，若出现错误提示请按照提示信息自行百度解决或再文章下留言我看到后会第一时间回复

但是问题又来了，关闭 `cmd` 窗口就会关闭 `Redis`，难道要一直开着吗？这显然是不科学的，下面看怎么在设置为自动运行：

```powershell
# 将 redis 设置为开机自动运行
redis-server --service-install redis.windows.conf
# 设置完成后运行 redis
redis-server --service-start
```

推荐一个可视化工具 [Redis Desktop Manager][8]

## 4. ThinkPHP5 配置 redis 缓存

在 `ThinkPHP5` 中 `config.php` 修改缓存方式为 `redis `

![配置图][9]

使用方法参考 [ThinkPHP5 开发手册缓存说明][10]

以上，就完成了 `Windows` 下 `ThinkPHP5` 配置 `redis` 缓存

溜了溜了，接着去开发了…

[1]: https://www.v2ex.com/
[2]: https://www.v2ex.com/t/251237
[3]: https://img.blanc.site/wiki/img/2.png
[4]: https://windows.php.net/downloads/pecl/releases/redis/
[5]: https://windows.php.net/downloads/pecl/releases/igbinary/
[6]: https://github.com/MSOpenTech/redis/releases
[7]: https://img.blanc.site/wiki/img/5.png
[8]: https://redisdesktop.com/download
[9]: https://img.blanc.site/wiki/img/4.png
[10]: https://www.kancloud.cn/manual/thinkphp5/118131