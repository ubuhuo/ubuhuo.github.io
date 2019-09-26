---
title: U盘安装 Linux distribution 提示 waring /dev/root does not exist
date: 2017/6/30
categories:
  - 操作系统
  - Linux
tags:
  - U盘安装 Linux 异常处理
copyright: true
---

之前也遇到过这种情况只是没有记录下来导致这次遇到的时候又百度了一遍，很麻烦，所以干脆就自己记录下来方便以后查询。

问题描述：U盘安装 CentOS 7 时提示 `waring /dev/root does not exist`

问题分析：这句错误指令是提醒我们电脑无法读取到U盘，我两次出现这个错误的原因都是因为U盘名字太长或太诡异。

个人解决办法[测试成功]：

## 第一步

用 Windows 系统打开U盘，选择属性，然后更改U盘的盘符标签（要求：尽可能的简短，英文）

![第一步][1]

## 第二步

进入U盘打开 EFI 文件夹，进入 boot 文件夹，修改 grub.cfg 文件里面的 `CentOS\x207\x20x86_64` 改为刚刚修改的U盘的盘符标签。

OK，处理完成，重启之后就可以安装了。

举一反三：本次问题是出现在安装 CentOS7 的时候，但解决问题的方法不仅仅可以用在这一个问题上，在遇到类似问题时注意联想。

[1]: https://www.blanc.site/img/27.png
[2]: https://www.blanc.site/img/28.png