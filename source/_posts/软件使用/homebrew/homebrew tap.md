---
title: homebrew tap
date: 2019/11/20
categories:
  - 软件使用
  - homebrew
tags:
  - homebrew tap
copyright: true
---

## homebrew 简介

`homebrew` 是使用 `ruby` 开发的 `MacOS` 软件包管理器

一般来说我们可以直接使用  `brew install xx` 命令安装我们需要的软件包，但也会有一些软件并没有包括在 `homebrew` 的核心库中，因此我们就需要一个命令来为 `homebrew` 添加第三方仓库源：`brew tap` 就是这个命令。

`brew tap` 可以将更多存储库添加到 `brew` 跟踪，更新和安装的列表中。

注：tap 是 third-party repositories 第三方库的简写

## brew tap 使用方式

```
# 不带任何参数直接使用命令时会列出当前使用的存储库
brew tap
```

```
# 默认情况下 brew 会对 https://github.com/user/repo 下的仓库进行浅拷贝
# 使用此命令之后我们就可以随意的使用 brew [un]install repo 安装和卸载软件包
# 也可以使用 brew update repo 进行软件包更新
brew tap <user>/<repo>
```

```
# 在本地对 URL 的仓库进行浅拷贝
# URL 可以是非 GitHub 的仓库也不需要必须是 HTTP 协议 只要是 Git 能够拉取的就行
brew tap <user>/<repo> URL
```

```
# 取消 brew 对给出仓库的监听
brew untap <user>/<repo> [<user>/<repo> <user>/<repo> ...]
```

更多场景的操作详情可以参考 [ brew 文档][1] 给出的介绍


## 参考

1. [brew 文档][1]
2. [segmentfault-一步-homebrew的tap功能详解][2]

[1]: https://docs.brew.sh/Taps
[2]: https://segmentfault.com/a/1190000012826983