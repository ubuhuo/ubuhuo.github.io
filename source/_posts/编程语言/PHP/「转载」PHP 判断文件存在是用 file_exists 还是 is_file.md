---
title: 「转载」PHP 判断文件存在是用 file_exists 还是 is_file
date: 2018/6/25
categories:
  - 编程语言
  - PHP
tags:
  - PHP 判断文件存在函数选择
copyright: true
abbrlink: '44e28494'
---

[原文链接](https://blog.csdn.net/zmzwll1314/article/details/51550016)

结论:
如果要判断文件是否存在，用函数 `is_file()`
如果要判断目录是否存在，用函数 `is_dir()`
好像没地方需要用 `file_exists` 了，不确定传入的参数是文件还是目录的时候用？

以下为原文作者的证明过程：

在写程序时发现在判断文件是否存在时，有两种写法，有的人用了 `is_file`，有的人用了 `file_exists`，用哪个更好或者说更合适呢？

判断文件存在用 `is_file` 还是 `file_exists`？

看了这篇 `PHP` 中 `file_exists` 与 `is_file`, `is_dir` 的区别的说法基本明白，`PHP` 的 `file_exists = is_dir + is_file`。

写程序验证一下：

分别执行 `1000次`，记录所需时间。

文件存在(当前目录)
`is_file: 0.4570ms`
`file_exists: 2.0640ms`

文件存在(绝对路径 `3` 层 `/www/hx/a/`)
`is_file: 0.4909ms`
`file_exists: 3.3500ms`

文件存在(绝对路径 `5` 层 `/www/hx/a/b/c/`)
`is_file: 0.4961ms`
`file_exists: 4.2100ms`

文件不存在(当前目录)
`is_file: 2.0170ms`
`file_exists: 1.9848ms`

文件不存在(绝对路径 `5` 层 `/www/hx/a/b/c/`)
`is_file: 4.1909ms`
`file_exists: 4.1502ms`

目录存在
`file_exists: 2.9271ms`
`is_dir: 0.4601ms`

目录不存在
`file_exists: 2.9719ms`
`is_dir: 2.9359ms`

`is_file($file)`
`file_exists($file)`
当 `$file` 是目录时，`is_file` 返回 `false`，`file_exists` 返回 `true`

文件存在的情况下，`is_file` 比 `file_exists` 要快得多；
要检测文件所在的目录越深，速度差越多，但至少快 `4` 倍。

文件不存在的情况下，`is_file` 比 `file_exists` 要慢一点点，但可以忽略不计。

目录存在的情况下，`is_dir` 比 `file_exists` 要快得多；
目录不存在的情况下，`is_dir` 比 `file_exists` 要慢一点点，但可以忽略不计。