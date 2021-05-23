---
title: Python 虚拟环境：virtualenv 以及 virtualenvwrapper
date: 2019/2/18
categories:
  - 1-网络笔记
tags:
  - 环境部署
  - Python
copyright: true
---

在使用 Python 开发的过程中，工程一多，难免会碰到不同的工程依赖不同版本的库的问题；亦或者是在开发过程中不想让物理环境里充斥各种各样的库，引发未来的依赖灾难。此时，我们需要对于不同的工程使用不同的虚拟环境来保持开发环境以及宿主环境的清洁。

## 1. virtualenv

`virtualenv` 可以在系统中建立多个不同并且相互不干扰的虚拟环境。

### 1.1 原理简介
把系统 `Python` 「复制」一份到 `virtualenv` 的环境，用命令 `source venv/bin/activate` 进入一个 `virtualenv` 环境时，`virtualenv` 会修改相关环境变量，让命令 `python` 和 `pip` 均指向当前的 `virtualenv` 环境。

PS：想了解更多可以查看 `activate` 脚本文件源码。

### 1.2 安装

```powershell
pip install virtualenv
```

### 1.3 使用

#### 1.3.1 需求：

我们有一个 `hello_world` 项目需要编写，但希望重新开辟一个 `Python` 包管理环境。

#### 1.3.2 假设：

项目地址：`/path/to/project/hello_world`

#### 1.3.3 创建虚拟环境：

```
virtualenv /path/to/project/hello_world
```

此时可以看到，在这个目录下面会有三个目录被建立:

```
lib -> 各种依赖库
include -> 虚拟环境中的头文件，包括 Python 的头文件
bin -> 虚拟环境中可用的命令包括开启虚拟环境的脚本 activate
```

#### 1.3.4 进入（激活）虚拟环境

```
source /path/to/project/hello_world/bin/activate
```

此时我们就进入到虚拟环境中了，接下来只需要安装项目需要的库即可。

PS：虚拟环境中的库与本机原始环境的库是不会相互干扰的。

#### 1.3.5 退出虚拟环境

```
deactivate
```

#### 1.3.6删除虚拟环境

如果想要删除虚拟环境，只要把这个目录下的 `bin include lib` 三个目录删掉就好了。

#### 1.3.7 其他选项

```
--version 显示当前版本号
-h, --help 显示帮助信息
-v, --verbose 显示详细信息
-q, --quiet 不显示详细信息
-p PYTHON_EXE, --python=PYTHON_EXE 指定所用的 python 解析器的版本
--clear 清空非 root 用户的安装，并重头开始创建隔离环境
--no-site-packages 令隔离环境不能访问系统全局的site-packages目录
--system-site-packages 令隔离环境可以访问系统全局的site-packages目录
--unzip-setuptools 安装时解压 Setuptools 或 Distribute
--relocatable 重定位某个已存在的隔离环境,使用该选项将修正脚本并令所有.pth文件使用相当路径
--distribute 使用 Distribute 代替 Setuptools，也可设置环境变量 VIRTUALENV_DISTRIBUTE 达到同样效果
--extra-search-dir=SEARCH_DIRS 用于查找 setuptools/distribute/pip 发布包的目录。可以添加任意数量的 –extra-search-dir 路径
--never-download 禁止从网上下载任何数据。此时，如果在本地搜索发布包失败， virtualenv 就会报错
--prompt==PROMPT 定义隔离环境的命令行前缀
```

## 2. virtualenvwrapper（基于 virtualenv）

`virtualenv` 的扩展工具，提供了一系列命令行命令，可以方便地创建、删除、复制、切换不同的虚拟环境。同时，使用该扩展后，所有虚拟环境都会被放置在同一个目录下。

`virtualenv` 的一个最大的缺点就是，每次开启虚拟环境之前要去虚拟环境所在目录下的 `bin` 目录下 `source` 一下 `activate`，这就需要我们记住每个虚拟环境所在的目录。

一种可行的解决方案是，将所有的虚拟环境目录全都集中起来，比如放到 `~/virtualenvs/`，并对不同的虚拟环境使用不同的目录来管理。`virtualenvwrapper` 正是这样做的。并且，它还省去了每次开启虚拟环境时候的 `source` 操作，使得虚拟环境更加好用。

### 2.1 安装

```
pip install virtualenvwrapper
```

### 2.2 配置

各操作系统配置方法不完全相同，请自行百度 Google。

这里只讲解一个下文会提到的配置中的概念：`WORKON_HOME`。

`WORKON_HOME` 是 `virtualenvwrapper` 配置过程中需要指定的一个环境变量，表示将要用来存放各虚拟环境目录的目录。

### 2.3 使用

#### 2.3.1 新建虚拟环境

```
mkvirtualenv <自定义虚拟环境名称>
```

这样会在 `WORKON_HOME` 变量指定的目录下新建名为 <自定义虚拟环境名称> 的虚拟环境。

若想指定 `Python` 版本，可通过 `--python` 参数指定想要使用的 `Python` 解释器。

```
mkvirtualenv --python=<Python 解释器路径> <自定义虚拟环境名称>
```

#### 2.3.2 查看存在的虚拟环境目录

```
workon
```

#### 2.3.3 进入（切换）虚拟环境

```
workon <虚拟环境名称>
```

#### 2.3.4退出虚拟环境

```
deactivate
```

#### 2.3.5删除虚拟环境

```
rmvirtualenv <虚拟环境名称>
```