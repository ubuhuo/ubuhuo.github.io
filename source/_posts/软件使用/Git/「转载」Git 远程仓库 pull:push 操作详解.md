---
title: 「转载」Git 远程仓库 pull/push 操作详解
date: 2017/8/17
categories:
  - 软件使用
  - Git
tags:
  - Git 远程仓库 pull/push
copyright: true
abbrlink: 6c235203
---

## 1. 简要说明使用 Git 操作远程仓库时 pull 和 push 命令机制及原理

`Git` 其实是是一个不用网络的仓库（本地仓库），你也可以把数据 `push` 到 `GitHub` 上（远程仓库）。

你现在的 `pull` 和 `push` 都是本地版本库和远程仓库之间的数据交互

## 2. 组成

在你的本地仓库，其实是由两部分组成：

```
1. 工作区 (Working Directory) //看得见的
2. 版本库 (Repository) //看不见的
    版本库又包括：
    * 暂存区(Stage)
    * 分支 (branch)
```

## 3. 流程：

### 3.1 初次提交：

1. 通过 `git add` 将文件 工作区 —>暂存区 (本地)
2. 通过 `git commit` 将文件 暂存区 —>分支 (本地)
3. 通过 `git push` 将文件 分支 —>远程库 (`GitHub`)

### 3.2 提交改动：

通过 `git commit` 将文件 暂存区 —>分支 (本地)
通过 `git push` 将文件 分支 —>远程库 (`GitHub`)

### 3.3 pull&push

通过 `git pull` 将文件 远程库 —>分支 (本地)
通过 `git push` 将文件 分支 —>远程库 (`GitHub`)

而上面的两个操作是需要有改动，有差异才能执行。
所以会提示暂存区和远程库的内容一致。

## 4. 原文

[原文地址请点这里][1]
[作者信息请点这里][2]
[学习Git请点这里][3]

[1]: https://segmentfault.com/q/1010000002664985/a-1020000002665956
[2]: https://segmentfault.com/u/qianjiahao
[3]: https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000