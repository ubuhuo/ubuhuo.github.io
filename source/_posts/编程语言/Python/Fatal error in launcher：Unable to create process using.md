---
title: Fatal error in launcher：Unable to create process using '"'
date: 2017/9/9
categories:
  - 编程语言
  - Python
copyright: true
---

`Windows` 下同时存在 `Python2` 和 `Python3` 使用 `pip` 时系统报错：`Fatal error in launcher: Unable to create process using '"'` 的解决方案

`Windows` 下 `Python3` 和 `Python2` 共存环境

用 `pip` 安装一个包
执行 `pip2 install xxx` 的时候报错

```
Fatal error in launcher: Unable to create process using '"'
```

执行 `pip3 install xxx` 的时候报同样的错误

```
Fatal error in launcher: Unable to create process using '"'
```

解决办法-改用如下命令：

```
# Python2 安装：
python2 -m pip install XXX
# Python3 安装
python3 -m pip install XXX
```

这样就可以正常使用 `pip` 安装模块了