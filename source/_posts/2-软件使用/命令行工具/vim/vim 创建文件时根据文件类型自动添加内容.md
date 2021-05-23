---
title: vim 创建文件时根据文件类型自动添加内容
date: 2020/04/27
categories:
  - 2-软件使用
  - 命令行工具
  - vim
tags:
  - vim
  - 效率工作
copyright: true
---

## 原理

修改 `.vimrc` 文件，利用 `autocmd` 命令在创建文件时运行指定函数

## 实现

```
" 以 shell 文件为例
" 添加函数

func SetShellNote()
    call setline(1, "/usr/bin/env bash")
endfunc

func SetTitle()
    call SetShellNote()
endfunc

" 使用 autocmd 自动调用
autocmd BufNewFile *.sh exec ":call SetTitle()"
```

## 参考

1. [halazi100：vim创建程序文件自动添加头部注释][1]

[1]: https://blog.csdn.net/halazi100/article/details/78245725