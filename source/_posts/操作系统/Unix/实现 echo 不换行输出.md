---
title: echo
date: 2020/04/28
categories:
  - 操作系统
  - Unix
tags:
  - Unix 命令
copyright: true
---

## 简介

`echo` 命令用于在 `shell` 中打印 `shell` 变量的值，或者直接输出指定的字符串。

语法：`echo [SHORT-OPTION]... [STRING]...`

## 详情

### SHORT-OPTION

1. `-n`：不输出换行
2. `-e`：开启对反斜线转移的解释
3. `-E`：取消对反斜线转义的解释（默认开启）

### 常见反斜线转义

| 表达方式 | 含义 |
| ------- | --- |
| `\a` | 发出警告声 |
| `\b` | 删除前一个字符 |
| `\c` | 最后不加上换行符号 |
| `\f` | 换行但光标仍旧停留在原来的位置 |
| `\n` | 换行且光标移至行首 |
| `\r` | 光标移至行首，但不换行 |
| `\t` | 插入 `tab` |
| `\v` | 与 `\f` 相同 |
| `\\` | 插入 `\` 字符 |
| `\nnn` | 插入 `nnn`（八进制）所代表的 `ASCII` 字符 |

## 具体问题

### 实现 echo 不换行输出

#### 使用场景

1. 编写 `shell` 脚本编写用户输入提示

#### 方法一：利用参数 -e

```powershell
# test.sh
echo -e "hello world\c"
```

#### 方法二：利用参数 -n

```powershell
# test.sh
echo -n "hello world"
```

## 参考
1. [lllxy：echo不换行的实现][1]
2. `man echo`

[1]: https://blog.csdn.net/lllxy/article/details/3423580