---
title: macOS 如何删除自带 ABC 输入法
date: 2019/8/3
categories:
  - 3-计算机科学
  - 2-计算机系统
  - 2-操作系统
  - macOS
tags:
  - macOS
  - 效率
copyright: true
---

## 环境

本人操作环境：
电脑型号：`联想 G50-80`
电脑系统：`macOS Mojave`

全驱动黑苹果

## 起因

1. `macOS` 自带的 `ABC` 输入法默认是不可删除的；
2. 输入法切换快捷键没办法设置成 `Shift`；

所以如果用 `ABC` + 原生拼音输入法，切换太麻烦
如果用可以通过 `Shift` 切换中英文的搜狗 + `ABC` 又总是会莫名其妙地被切换到 `ABC`

## 删除过程

1. 下载一个能打开 `.plist` 文件的软件（[PlistEdit Pro][1] 或者 `XCode` 都行）
2. 将当前输入法切换到 `ABC` 输入法
3. 通过 `PlistEdit Pro` 或者 `XCode` 打开 `~/Library/Preferences/com.apple.HIToolbox.plist`
4. 依次点开 `Root` - `AppleEnabledInputSources` 会看到很多选项，如下图：

![通过 PlistEdit Pro 打开文件][2]

5. 依次点开这些选项找到 `Value` 栏为 `ABC` 的选项删除掉
6. 通过「访达」找到该文件，快捷键 `Command + i` 打开文件简介勾选「已锁定」
7. 注销或重启 

## 验证是否成功

打开「系统偏好设置」-「键盘」-「输入法」查看是否已删除：

![查看是否成功删除][3]

好了，删除成功了，可以开心的通过 `Shift` 切换中英文了

以上。

[1]: https://www.fatcatsoftware.com/plisteditpro/
[2]: https://img.blanc.site//wiki/img/20190803211622.png
[3]: https://img.blanc.site//wiki/img/20190803211621.png