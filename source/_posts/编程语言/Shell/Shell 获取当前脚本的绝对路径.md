---
title: Shell 获取当前脚本的绝对路径
date: 2020/04/26
categories:
  - 编程语言
  - Shell
tags:
  - Shell
copyright: true
---

## 1. dirname + pwd

```powershell
root_absolute_dir=$(cd "$(dirname "$0")";pwd)
```

## 2. readlink

```powershell
root_absolute_dir=$(dirname $(readlink -f "$0"))
```

### 注意

`macOS` 中使用 `readlink -f` 命令会有如下报错：

```powershell
readlink: illegal option -- f
usage: readlink [-n] [file ...]
```

可以安装 `greadlink` 代替 `readlink`

```powershell
# 这里仅提供使用 Homebrew 安装方法
brew install greadlink
# 可以修改 bash_profile 文件，添加 alias readlink=greadlink
# 使用 greadlink 代替 readlink
```

## 参考

1. [10km：bash shell:获取当前脚本的绝对路径(pwd/readlink)][1]
2. [orientlu：shell 当前工作目录的绝对路径][2]
3. [macos - 如何在Mac上获取GNU的readlink -f的行为？][3]

[1]: https://blog.csdn.net/10km/article/details/51906821
[2]: https://www.jianshu.com/p/b2a689bcc76c
[3]: https://www.itranslater.com/qa/details/2109562218713973760