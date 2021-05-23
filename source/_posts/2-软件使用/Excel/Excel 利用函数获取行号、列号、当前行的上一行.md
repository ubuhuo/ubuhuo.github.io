---
title: Excel 利用函数获取行号、列号、当前行的上一行
date: 2019/8/23
categories:
  - 2-软件使用
  - Excel
tags:
  - Excel
copyright: true
---

## 获取行号

`=ROW()`

## 获取列号

`=COLUMN()`

## 获取上一行的内容

`=INDIRECT("A"&ROW()-1)`