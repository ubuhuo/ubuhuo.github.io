---
title: 解决 TP5 无法接收客户端 Post 传递的 Json 参数
date: 2018/4/23
categories:
  - 2-软件使用
  - ThinkPHP
tags:
  - ThinkPHP
copyright: true
---

`ThinkPHP` 版本： `5.0.16`

这段时间在学习 `API` 开发，从网上搞了一个课程在跟着做，就在昨天我又一次碰到了讲师一路畅通我半天调试的情况，遇到的问题是我用 `Postman` 测试 `API` 某 `Post` 接口的时候无论怎么发送请求总是提示我参数验证失败，在我调试的过程中某次采用通过 `Body` 表传递参数的方法，`API` 神奇地通了，于是我判断是之前 `Json` 传递出了问题，就各种在网上搜，然而并没有解决方法[不过 `Get` 了两个知识点放在文章结尾处]。

关于 `ThinkPHP5` 的问题度娘不行就只能查源代码了，于是乎我就跟着错误一点一点的打断点终于被我发现了问题所在：

![问题源码][1]

原来 `ThinkPHP5` 是通过 `strpos` 函数在 `Header` 的 `content-type` 值中查找 `app/json` 字符串，也就是说你必须设置 `Header` 的 `content-type` 值为 `app/json` 才行，否则 `ThinkPHP5` 不会认为你传入的是 `Json` 参数并给 `Request::post` 设值为空。

以上就解决了 `ThinkPHP5` 无法接收 客户端 `Post` 传递的 `Json` 参数的问题。

两个知识点：

1. `content-type` 为 `"application/json"` 的数据 `PHP` 是不能直接识别的，所以导致 `$_POST` 数组为空。解决办法：我们只要用 `$GLOBALS['HTTP_RAW_POST_DATA']` 把原始数据取出来，然后再 `json_decode` 就行了[使用此方法，需要设置 `php.ini` 中的`always_populate_raw_post_data` 值为 `$HTTP_RAW_POST_DATA` ，包含了 `POST` 的原始数据。但这不是一个超全局变量，要在函数中使用它，必须声明为 `global` ，或使用 `$GLOBALS['HTTP_RAW_POST_DATA']` 代替。；
2. 可以通过 `file_get_contents('php://input')` 代替上面的方法直接获取到传入的 `Json` 参数[我在阅读 `ThinkPHP5` 源码的时候发现他们也是用这个方法来获取的初始值]。

[1]: https://img.blanc.site/wiki/img/12.png