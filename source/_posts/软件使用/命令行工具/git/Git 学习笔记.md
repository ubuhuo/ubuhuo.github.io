---
title: Git 学习笔记
date: 2017/7/25
categories:
  - 软件使用
  - 命令行工具
  - git
tags:
  - git
  - 学习笔记
copyright: true
---

## 创建版本库

什么是版本库呢？版本库又名仓库，英文名 `repository`，你可以简单理解成一个目录，这个目录里面的所有文件都可以被 `Git` 管理起来，每个文件的修改、删除，`Git` 都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

所以，创建一个版本库非常简单，首先，选择一个合适的地方，创建一个空目录：

```powershell
$ mkdir learngit
$ cd learngit
$ pwd
```

把一个文件放到 `Git` 仓库只需要两步：

第一步，用命令 `git add` 告诉 `Git`，把文件添加到仓库：

```powershell
$ git add <The file name>
```

第二步，用命令 `git commit` 告诉 `Git`，把文件提交到仓库：

```powershell
$ git commit -m "You can write directions here"
```

## Git时光机

要随时掌握工作区的状态，使用 `git status` 命令。
如果 `git status` 告诉你有文件被修改过，用 git diff` 可以查看修改内容。

`HEAD` 指向的版本就是当前版本，上一个版本就是 `HEAD^`，上上一个版本就是 `HEAD^^`，当然往上 `100` 个版本写 100` 个 `^` 比较容易数不过来，所以写成 `HEAD~100`。因此，`Git`允许我们在版本的历史之间穿梭，使用命令 `git reset --hard <commit_id>`。

穿梭前，用 `git log` 可以查看提交历史，以便确定要回退到哪个版本。

要重返未来，用 `git reflog` 查看命令历史，以便确定要回到未来的哪个版本。

工作区和暂存区概念以及 `add` 和 `commit` 工作原理

![add和commit原理][1]

[1]: https://www.blanc.site/img/46.jpg