---
title: pip 自动生成或安装 requiremen 依赖
date: 2019/10/9
categories:
  - 软件使用
  - pip
tags:
  - pip
copyright: true
---

生成 `requirements.txt` 文件

```powershell
pip freeze > requirements.txt
```

安装 `requirements.txt` 依赖

```powershell
pip install -r requirements.txt
```