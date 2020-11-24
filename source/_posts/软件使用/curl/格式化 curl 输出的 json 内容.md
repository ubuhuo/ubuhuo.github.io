---
title: 格式化 curl 输出的 json 内容
date: 2019/11/21
categories:
  - 软件使用
  - curl
tags:
  - curl 格式化输出
copyright: true
---

在 `API` 调试过程中除了使用 `GUI` 工具（类似：`Postman`）我最常使用的就是 `curl` 命令了 ，简单快捷，但是 `curl` 的输出结果不是特别友好，特别是 `json` 格式，会在命令行里输出成一个长字符串，如何解决那？

## 使用 Python 工具格式化

```
curl https://test.com/api/test | python -m json.tool
```

## 使用 npm json 包格式化

```
npm install -g json
curl https://test.com/api/test | json
```

## 隐藏 curl 统计信息

在使用上面的格式化命令时，curl 会在输出结果前先输出一段统计信息类似：

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   427  100   427    0     0  17300      0 --:--:-- --:--:-- --:--:-- 17791
```

我们可以使用 curl 的 -s 参数来隐藏这段统计信息：

```
curl -s https://test.com/api/test | python -m json.tool

curl -s https://test.com/api/test | json
```

## 参考
1. [简书-流水不腐小夏-格式化Curl返回的Json字符][1]

[1]: https://www.jianshu.com/p/7bcb700cd235
