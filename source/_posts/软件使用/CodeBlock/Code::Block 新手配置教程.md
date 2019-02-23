---
title: 'Code::Block 新手配置教程'
date: 2017/3/13
categories:
  - 软件使用
  - CodeBlock
tags:
  - 'Code::Block 新手配置教程'
copyright: true
abbrlink: 51f295ff
---

## 1. 前言

总有些东西要写在前面

### 1.1 有关 Code::Block

`Code::Blocks` 是一个开放源码的全功能的跨平台 `C/C++` 集成开发环境。 `Code::Blocks` 是开放源码软件。`Code::Blocks` 由纯粹的 `C++` 语言开发完成，它使用了著名的图形界面库 `wxWidgets 2.6.2 unicode`。对于追求完美的 `C++` 程序员，再也不必忍受 `Eclipse` 的缓慢，再也不必忍受 `VS.NET` 的庞大和高昂的价格。

### 1.2 为什么要用Code::Block

这段时间在网易云上自学 `C 语言`：[老九学堂][1]（吐血安利）。里面的配套编译器就是 `Code::Block`。

## 2. 下载

各操作系统下载地址：

### 2.1 Windows XP / Vista / 7 / 8.x / 10:

推荐下载：[codeblocks-16.01mingw-setup.exe][2]

### 2.2 Linux：

`Linux` 系统太多而且我也没用过就不推荐了：全部[下载地址][3]，需要自取。

### 2.3 Mac OS X:

只有这一个版本：[CodeBlocks-13.12-mac.zip][4]

## 3. 配置

包括：汉化-语法高亮-调整字体等常见配置

### 3.1 汉化

#### 3.1.1 先百度下载汉化包

#### 3.1.2 替换文件

找到你 `Codeblocks` 的安装根目录，依次进入 `share / CodeBlocks` 文件夹，解压汉化包，其中的 `locale` 文件复制到 `CodeBlocks` 中。

#### 3.1.3 汉化：

如果全程你都没有打开 `Codeblocks` 编译器，这个时候你直接打开即可汉化成功；

反之：打开软件，`Settings-Evironment`，左边列表选择 `View`，语言选项改成 `Chinese`，重启即可完成汉化操作（也可直接关掉再启动 `Codeblocks` 编译器）。

### 3.2 语法高亮

#### 3.2.1 百度下载配置文件

#### 3.2.2 替换配置文件：

下载好配置文件，并将其添加到 `C:\Users\<你的用户名>\AppData\Roaming\CodeBlocks\（覆盖以前的配置文件）`。

注意：在添加这些自定义配置文件之前一定要先将之前的文件配置备份！在添加时一定要确保 `Code::Blocks` 没有在运行！

#### 3.2.3 设置语法高亮：

添加好配置文件之后，运行 `Code::Blocks`。打开 `setting`（设置）=> `Editor`（编译器）=> `Syntax highlighting`（语法高亮）=> `colour theme`（色彩主题），这时你就可以根据个人的品味来选择你喜欢的主题了！
补充：当你修改好主题之后，可能会看不到你的光标，其实这是因为你的光标颜色选的不合适。找到 `setting`（设置）=> `Editor`（编译器）=> `Margins and caret`（边界和光标），你就可以修改光标的宽度和颜色了。

### 3.3 调整字体

`setting`（设置）=> `Editor`（编译器）=> 常规设置第一个就是字体，点击选择即可调整字体。

### 3.4 等

其实汉化之后很多功能都可以自己去摸索出来，如果大家还有什么比较棘手的问题欢迎在留言区与我讨论，当然亦可以通过邮件联系我。

## 4. 总思

有个东西百度，但还有个东西叫脑子，希望大家遇到问题能多动脑子。

[1]: http://study.163.com/course/courseMain.htm?courseId=1003425004
[2]: http://sourceforge.net/projects/codeblocks/files/Binaries/16.01/Windows/codeblocks-16.01mingw-setup.exe
[3]: http://www.codeblocks.org/downloads/26#windows#line1
[4]: http://sourceforge.net/projects/codeblocks/files/Binaries/13.12/MacOS/CodeBlocks-13.12-mac.zip