---
title: zsh 历史文件损坏
date: 2020/10/17
categories:
  - 2-软件使用
  - zsh
tags:
  - zsh
copyright: true
---

系统因为某些原因强行重启的时候有很大可能损坏 zsh 的历史记录文件，导致下次使用时提示：`zsh: corrupt history file /path/to/.zsh_history` 

理论上来说直接删除 `.zsh_history` 是可以消除报错的，但会导致之前的命令历史被删除，正确的解决方法是：

```powershell
cp .zsh_history zsh_history			// 复制损坏的历史记录文件
rm .zsh_history						// 删除原来的历史记录
strings zsh_history > .zsh_history	// 通过 strings 命令修复此文件
```