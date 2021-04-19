---
title: 利用本地缓存解决 pyenv 下载缓慢问题
date: 2020/04/13
categories:
  - 软件使用
  - 命令行工具
  - pyenv
tags:
  - pyenv
copyright: true
---

从 [python ftp 网站][1] 下载好需要目标版本的 `python` 压缩包，然后将其放在 `~/.pyenv/cache` 中。

再使用 `pyenv intsall` 指令，`pyenv` 就会判断下载已完成，自动开始进行安装。

[1]: https://www.python.org/ftp/python/