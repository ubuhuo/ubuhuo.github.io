---
title: Shell 区分不同 Unix 系统
date: 2020/04/29
categories:
  - 编程语言
  - Shell
tags:
  - Shell
copyright: true
---

## 区分 macOS 和 Linux

```powershell
OS=`uname -s`
if [ ${OS} == "Darwin"  ];then
    sudo brew install git
elif [ ${OS} == "Linux"  ];then
    ...
```

## 区分不同 Linux 系统

在 `Linux` 系统中有一个记录 OS 的发行版本的 `os-release` 文件，位置在 `/etc/os-release`。可以利用 `source /etc/os-release` 将文件中的 `key-value` 数据导入到上下文中，然后通过不同系统 `ID` 值不同的特性进行区分。

```powershell
source /etc/os-release
case $ID in
    debian|ubuntu|devuan)
        sudo apt-get install git
        ;;
    centos|fedora|rhel)
        yumdnf="yum"
        if test "$(echo "$VERSION_ID >= 22" | bc)" -ne 0; then
            yumdnf="dnf"
        fi
        sudo $yumdnf install -y git
        ;;
    *)
        exit 1
        ;;
esac
```

## 参考
1. [温欣爸比：如何判断 Unix 系统 OS 版本][1]
2. [The Linux Command Line：流程控制：case 分支][2]

[1]: https://wxnacy.com/2017/11/24/get-os-name/
[2]: http://billie66.github.io/TLCL/book/chap32.html