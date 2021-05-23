---
title: ThinkPHP5 静态资源[CSS , JS , img]加载
date: 2018/3/5
categories:
  - 2-软件使用
  - ThinkPHP
tags:
  - ThinkPHP
copyright: true
---

说起这个静态资源加载可真的是一把心酸泪呀，各种路径问题[绝对、相对]，还有前段时间写 `WordPress` 主题也是，需要通过各种内置钩子或函数模版来解决路径问题；

解决 `ThinkPHP5` 的 `CSS` , `JS` 静态资源加载我采用的是官方手册给出的视图模版函数 `{load href="/..." /}` 在视图模版中引入此函数并设置静态资源位置，函数会自动识别引入文件是 `CSS` 文件还是 `JS` 文件从而选择不同的引入方式；[官方手册本章节地址][1]

注意 `href="/..."` 中 `/` 指的是 `public` 目录 [即入口目录]

解决 `ThinkPHP5` 的其他静态资源[图片等]加载我采用的是设置视图输出字符串内容替换，即修改 `app/config.php` 下的 `view_replace_strview_replace_str` 具体修改方法请参考：[官方手册本章节地址][2]

[1]: https://www.kancloud.cn/manual/thinkphp5/125020
[2]: https://www.kancloud.cn/manual/thinkphp5/118120