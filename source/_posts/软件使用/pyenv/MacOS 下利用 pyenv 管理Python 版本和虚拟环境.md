---
title: MacOS 下利用 pyenv 管理Python 版本和虚拟环境
date: 2019/2/22
categories:
  - 软件使用
  - pyenv
tags:
  - MacOS pyenv 使用教程
copyright: true
---

## pyenv 介绍

`pyenv` 是 `Python` 版本管理工具。`pyenv` 可以改变全局的 `Python` 版本，安装多个版本的 `Python`，设置目录级别的 `Python` 版本，还能创建和管理 `virtual python environments`。所有的设置都是用户级别的操作，不需要 `sudo` 命令。

`pyenv` 主要用来管理 `Python` 的版本，比如一个项目需要 `Python 2.x`，一个项目需要 `Python 3.x`。 而 `virtualenv` 主要用来管理 `Python` 包的依赖，不同项目需要依赖的包版本不同，则需要使用虚拟环境。

## pyenv 原理简介

`pyenv` 通过系统修改环境变量来实现 `Python` 不同版本的切换。而 `virtualenv` 通过将 `Python` 包安装到一个目录来作为 `Python` 包虚拟环境，通过切换目录来实现不同包环境间的切换。

`pyenv` 的美好之处在于，它并没有使用将不同的 `PATH` 植入不同的 `shell` 这种高耦合的工作方式，而是简单地在 `PATH` 的最前面插入了一个垫片路径（`shims`）：`~/.pyenv/shims:/usr/local/bin:/usr/bin:/bin`。所有对 `Python` 可执行文件的查找都会首先被这个 `shims` 路径截获，从而使后方的系统路径失效。

## 安装之前

不同系统请参考 [Common build problems][1]，安装必须的工具。

## pyenv 安装

根据官网的 [安装说明][2] 或者 [自动安装][3]。 如果使用 `Mac` 直接使用 `Homebrew`。安装成功后记得在 `.bashrc` 或者 `.bash_profile` 中添加三行来开启自动补全。

```powershell
# 根据自己的环境配置
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

### 自动安装

`pyenv` 提供了自动安装的工具，执行命令安装即可：

```powershell
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```

保证系统有 `git`，否则需要新安装 `git`。

### 手动安装

如果想要更加详细的了解安装过程，可以使用手动安装。将 `pyenv` 检出到你想安装的目录。建议路径为：`$HOME/.pyenv`

```powershell
cd ~
git clone git://github.com/yyuu/pyenv.git .pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
```

添加环境变量 `PYENV_ROOT` 指向 `pyenv` 检出的根目录，并向 `$PATH` 添加 `$PYENV_ROOT/bin` 以提供访问 `pyenv` 命令的路径。

这里的 `shell` 配置文件（`~/.bash_profile`）依不同 `Linux` 而需作修改，如果使用 `zsh` 则需要相应的配置 `~/.zshrc`

在使用 `pyenv` 之后使用 `pip` 安装的第三方模块会自动安装到当前使用 `Python` 版本下，不会和系统模块产生冲突。使用 `pip` 安装模块之后，如果没有生效，记得使用 `pyenv rehash` 来更新垫片路径。

## pyenv 常用命令

使用 `pyenv commands` 显示所有可用命令

### Python 配置

```powershell
# 查看系统当前安装的python列表
pyenv versions

# 列出可安装版本
pyenv install --list 

# 安装指定版本 Python
pyenv install -v 3.5.1

# 卸载指定版本 Python
pyenv uninstall 2.7.3

# 显示当前python安装路径
pyenv which python 

# 创建垫片路径
# 为所有已安装的可执行文件创建 shims
# 如：~/.pyenv/versions/*/bin/*
# 每当你增删了 Python 版本或带有可执行文件的包（如 pip）以后，都应该执行一次本命令
pyenv rehash
```


### Python 切换

```powershell
#  设置全局的 Python 版本，通过将版本号写入 ~/.pyenv/version 文件的方式
pyenv global <版本号>

# 设置面向程序的本地版本，通过将版本号写入当前目录下的 .python-version 文件的方式。通过这种方式设置的 Python 版本优先级较 global 高。
# pyenv 会从当前目录开始向上逐级查找 .python-version 文件，直到根目录为止。若找不到，就用 global 版本。
pyenv local <版本号>

# 设置面向 shell 的 Python 版本，通过设置当前 shell 的 PYENV_VERSION 环境变量的方式。这个版本的优先级比 local 和 global 都要高。
pyenv shell <版本号>

# --unset 参数可以用于取消当前 shell 设定的版本。
pyenv shell --unset
```

### Python 优先级

`shell > local > global`

## pyenv-virtualenv

`pyenv-virtualenv` 是 `pyenv` 下基于 `virtualenv` 的一款插件，通过 `pyenv-virtualenv` 插件可以很好的和 `virtualenv` 结合。

### 安装

使用自动安装 pyenv 后，它会自动安装部分插件，其中包括了 `pyenv-virtualenv`。

若使用 `Homebrew` 进行安装，则可使用命令 `brew install pyenv-virtualenv` 安装 `pyenv-virtualenv` 插件。

这样安装之后需要执行如下配置：

```shell
# 使用 zsh shell
vim ~/.zshrc

# 使用系统默认
vim ~/.bash_profile

# 在 `.zshrc` 或 `.bash_profile` 文件最后写入：
# pyenv-virtualenv
if which pyenv-virtualenv-init > /dev/null;
  then eval "$(pyenv virtualenv-init -)";
fi

# 使配置生效
source ~/.zshrc
# or
source ~/.bash_profile
```

### 使用

```powershell
# 创建虚拟环境
# 若不指定 Python 版本，默认使用当前环境 Python 版本
pyenv virtualenv <Python 版本号> <虚拟环境名称>

# 列出当前虚拟环境
pyenv virtualenvs

# 激活虚拟环境
pyenv activate <虚拟环境名称>

# 退出虚拟环境
pyenv deactivate

# 删除虚拟环境
pyenv uninstall <虚拟环境名称>
```

## Tips：更换 pip 源

因为国内网络环境，如果在局域网内 `pip` 下载慢，可以尝试使用阿里云提供的镜像，创建 `vim ~/.pip/pip.conf`，然后填入：

```powershell
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

更多 `pip` 国内源：

豆瓣：`http://pypi.douban.com`
阿里云：`http://mirrors.aliyun.com/pypi/simple`
清华大学：`https://pypi.tuna.tsinghua.edu.cn/simple`


## 参考

* [pyenv GitHub][4]
* [使用 pyenv 管理 Python 版本][5]
* [使用 pyenv + virtualenv 打造多版本 Python 开发环境][6]
* [Mac 下 pyenv 与 pyenv-virtualenv 的安装和使用][7]

[1]: https://github.com/pyenv/pyenv/wiki/Common-build-problems
[2]: https://github.com/yyuu/pyenv#installation
[3]: https://github.com/yyuu/pyenv-installer
[4]: https://github.com/pyenv/pyenv
[5]: http://einverne.github.io/post/2017/04/pyenv.html
[6]: https://segmentfault.com/a/1190000005859547
[7]: https://github.com/eteplus/blog/issues/4