---
title: VS Code + PHPStudy 断点调试 PHP
date: 2018/4/3
categories:
  - 2-软件使用
  - VSCode
tags:
  - VSCode
copyright: true
---

最近可利用时间比较少，加上团队项目重新开发[没错之前的网站白做了，所以主题开发就鸽了…

目前正在尝试做三端分离的小程序开发[借鉴慕课网某课程需要的可以找我要]，讲师“嘴欠”非得讲什么断点调试，我这个敲代码至今没调试过代码的人也是突然燃起了兴趣，就跟着设置了一下，仍然是常见的软件不匹配问题：就是讲师叭叭叭三两句话弄好了我这边还没开始…

经过度娘的一番调教大概弄懂了具体操作流程了，本次教程以 `VS Code + PHPStudy` 为例，但适用于所有可以进行 `PHP` 断点调试的编程软件：

1. 打开 `phpinfo` 查看是否安装了 `XDebug PHP` 插件[ `PHPStudy` 一般是不自带或者不自动开启的]
2. 若没有安装，[点击这里][1]，然后将你的 `phpinfo` 页面源码复制进输入框，让网站帮你查找适合你版本的插件[我当时搜索的时候提示的是只支持 `PHP 7.0` 以上的版本，不晓得是不是我老花眼…]
3. 按照网站给出的提示把插件移动到对应的文件夹，并且修改 `PHP` 的配置文件[`php.ini`] 操作如图：

![下载指导][2]

![打开 php.ini][3]

4. 把下面这两句也写进 `php.ini` 文件[作用是开启远程调试]

```ini
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```

5. 重启 `Apache` 服务器[到此 `PHP` 就设置好了]
6. 打开 `VS Code` 下载 `PHP Debug`、`PHP Extension Pack`、`PHP IntelliSense` 三个插件
7. 打开 `VS Code` 设置文件设置 `PHP` 路径，如图：

![设置 PHP 路径][4]

8. 重启 `VS Code` 随便打开一个 `PHP` 文件按 `F5` 开始调试

[1]: https://xdebug.org/wizard.php
[2]: https://img.blanc.site/wiki/img/27.png
[3]: https://img.blanc.site/wiki/img/28.png
[4]: https://img.blanc.site/wiki/img/29.png