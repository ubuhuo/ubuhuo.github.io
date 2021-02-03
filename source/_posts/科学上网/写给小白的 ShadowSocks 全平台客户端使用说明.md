---
title: 写给小白的 ShadowSocks 全平台客户端使用说明
date: 2020/01/20
categories:
  - 科学上网
tags:
  - ShadowSocks
copyright: true
---

## 前言

自己搭了一个 `SSR` 服务器，平时周围的朋友想要翻墙的时候也都会来找我，他们和我一起承担服务器费用，我教他们学会翻墙，大家一起心系自由，本来是挺好的一个事情，但是每当有一个新朋友要加入的时候我就得手把手的教他一遍，还得尽可能的通俗的描述。

基于以上原因，我写下了本篇文章，本意是给我身边想要翻墙却比较电脑小白的朋友，如果也恰好帮助了你，便也是缘分吧。

## Windows

教程截图来自本人的 `Win10` 系统，其他 `Windows` 系统也是类似的

我本人常用的是来自 [shadowsocks.org][1] 的 [shadowsocks-win][2]

可以点击 [此链接][2] 到 `Github` 下载最新版本（下载速度会比较慢）

也可以点击 [此链接，提取码: jaf7][8] 到我的百度云盘分享下载（版本是我写这篇文章时的最新版本）

以上链接如果都无法下载请 `Email` 我：`ryomahan1996@gmail.com`

获取到软件压缩包后将里面的内容解压到一个文件夹中

我一般会使用压缩软件的解压此压缩包到 `XX` 文件夹中

![解压压缩包][3]

打开解压好的文件夹就能看到我们翻墙所需要的客户端了（切记就算你的解压文件没有压缩到 `xx` 文件夹这个功能也要在解压后第一时间将此程序放置在一个空的文件夹中，因为程序在运行过程中会产生一些配置文件，如果直接裸露的放在桌面你的桌面会变得一团糟）

![打开解压好的文件夹][4]

双击运行客户端，就能看到程序界面，并且在底部右侧状态栏中能看到灰色小飞机的图标（没有的话可能是被隐藏在不活跃程序里了）

![开启程序][5]

将 `SSR` 服务提供方（也就是我）给出的配置内容填写在对应位置，点击确定按钮

![配置代理][6]

然后右键底部状态栏里的灰色小飞机图标，将鼠标指向 `系统代理` 点击 `PAC模式` 也可以顺手勾选上 `开机启动`

![开启代理][7]

到此就可以正常使用` SSR` 服务啦，快打开浏览器输入 `google.com` 看看吧

### 全局模式与PAC模式的区别

简单地说，在全局模式下，所有网站默认走代理。而 `PAC模式` 是只有被墙的才会走代理，推荐 `PAC模式`，如果 `PAC模式` 无法访问一些网站，就换全局模式试试，一般是因为 `PAC` 更新不及时（也可能是 `GFWList` 更新不及时）导致的。

## Mac

教程截图来自本人的黑苹果，其他 `macOS` 系统也是类似的

我本人常用的是来自 [shadowsocks.org][1] 的 [ShadowsocksX-NG][9]

可以点击 [此链接][9] 到 `Github` 下载最新版本（下载速度会比较慢）

也可以点击 [此链接，提取码: 8tgr][10] 到我的百度云盘分享下载（版本是我写这篇文章时的最新版本）

以上链接如果都无法下载请 `Email` 我：`ryomahan1996@gmail.com`

获取到软件之后将其移动至应用程序文件夹

![移动到应用程序文件夹][11]

从启动台打开应用后软件会在后台运行并在菜单栏显示图标，左键小飞机图标就能看见菜单：

![查看小飞机菜单][12]

而后将鼠标移动至「服务器」栏目并选择「服务器设置」然后点击「+」按钮来添加你的 `Shadowsocks` 配置：

![添加配置][13]

在几处必填选项填写上 `SSR` 服务提供方给出的配置内容，并在备注处填写一个自己喜欢的名称，然后点击确定按钮即可：

![填写配置][14]

配置完成后再次左键小飞机图标打开菜单移动到「服务器」栏目并选择你刚刚添加上的服务器配置：

![选择配置][15]

然后就可以选择打开 `Shadowsocks` 开始你的自由之旅啦~

全局模式与 `PAC模式` 的区别参考 `Windows` 章节。

## Android

手里暂时没有机器，以后再说吧。

## IOS

教程截图来自本人的 `iPhone X IOS13` ，其他机型也是类似的

如果想在 `IOS` 设备上使用 `Shadowsocks` 的话，首先你需要有一个非大陆的 `apple ID` 用来下载软件，至于如何获取非大陆的 `apple ID` 可以自己去百度，这里我也给出一个连接：[点击这里查看][16]

首先在 `App Store` 里登录你的非大陆账号（主需要在 `App Store` 里登录就行）：

![登录账号][17]

选择一个免费的 Shadowsocks 客户端，这里我推荐使用 Potatso Lite，免费而且好用：

![下载软件][21]

现在完成之后打开软件进行配置：

![配置软件1][23]

![配置软件2][20]

配置完成后进入软件主界面选择自己的配置然后点击小飞机图标就可以运行啦：

![运行软件][22]

这里还有一个小技巧，Potatso Lite 软件自带了一个 IOS 小工具，可以在手机的负页（主界面再往左滑一下）进行添加：

![添加小工具][18]

![使用小工具][19]

这样我们只需要在使用的时候进入到负页开启就行了，不用再进入软件进行操作。

## Linux

`Linux` 系统我就不多说了，你都能用 `Linux` 了还不会配置 `SSR` 客户端的话就说不过去了

[1]: https://shadowsocks.org/en/download/clients.html
[2]: https://github.com/shadowsocks/shadowsocks-windows/releases
[3]: https://img.blanc.site/wiki/img/20191122070012.png
[4]: https://img.blanc.site/wiki/img/20191122070416.png
[5]: https://img.blanc.site/wiki/img/20191122070900.png
[6]: https://img.blanc.site/wiki/img/20191122071152.png
[7]: https://img.blanc.site/wiki/img/20191122071247.png
[8]: https://pan.baidu.com/s/1iAK-rEsT4XEXfYUhg8rumw
[9]: https://github.com/shadowsocks/ShadowsocksX-NG/releases
[10]: https://pan.baidu.com/s/1J0_QtVZ7haWcCCb5zTWmRg
[11]: https://img.blanc.site//wiki/img20200222225758.png
[12]: https://img.blanc.site//wiki/img20200222231209.png
[13]: https://img.blanc.site//wiki/img20200222231258.png
[14]: https://img.blanc.site//wiki/img20200222231332.png
[15]: https://img.blanc.site//wiki/img20200222231420.png
[16]: https://zhuanlan.zhihu.com/p/36574047
[17]: https://img.blanc.site//wiki/img20200222232629.png
[18]: https://img.blanc.site//wiki/img20200222233058.png
[19]: https://img.blanc.site//wiki/img20200222233210.png
[20]: https://img.blanc.site//wiki/img20200222233253.png
[21]: https://img.blanc.site//wiki/img20200222233315.png
[22]: https://img.blanc.site//wiki/img20200222233511.png
[23]: https://img.blanc.site//wiki/img20200222233416.png