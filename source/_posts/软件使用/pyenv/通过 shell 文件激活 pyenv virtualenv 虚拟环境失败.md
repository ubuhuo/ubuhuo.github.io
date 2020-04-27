---
title: 通过 shell 文件激活 pyenv virtualenv 虚拟环境失败
date: 2020/04/27
categories:
  - 软件使用
  - pyenv
tags:
  - pyenv
copyright: true
---

## 错误复现

```powershell
# test.sh
pyenv acitvate env_name
```

```powershell
>./test.sh

Failed to activate virtualenv.

Perhaps pyenv-virtualenv has not been loaded into your shell properly.
Please restart current shell and try again.
```

## 错误原因

`pyenv` 和 `pyenv-virtualenv` 未加入环境变量

## 解决方案

```powershell
# test.sh
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
pyenv acitvate env_name
```