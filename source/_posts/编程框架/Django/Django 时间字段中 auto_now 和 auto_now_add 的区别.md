---
title: Django 时间字段中 auto_now 和 auto_now_add 的区别
date: 2020/04/18
categories:
  - 编程框架
  - Django
tags:
  - Django 笔记
copyright: true
---


## auto_now

相应字段在被保存时会自动保存为当前时间。

注：每次对其实例执行 `save()` 操作时都令当前字段保存为当前时间。

## auto_now_add

无论是否赋值，相应实例第一次保存的时都会保存为当前时间。

注：之后可以手动修改。