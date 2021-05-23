---
title: 解决 Vue CSS 样式重复载入，为 Vue 添加全局 less 或 sass 基础样式库
date: 2019/8/27
categories:
  - 2-软件使用
  - Vue
tags:
  - Vue
copyright: true
---

## 诉求

项目开发使用了 `iView` 组件库，在开发过程中想自定义 `iView` 主题但是按照 `iView` 官方推荐「变量覆盖」方法配置完后会出现 `CSS` 样式重复载入的情况，如下图：

![CSS 重复载入][1]

网上包括 `Vue CLI` 官方都是推荐使用 `style-resources-loader` 进行「开发环境」自动化导入，但是你会发现按照给出的方法配置完之后还是会出现 `CSS` 样式重复的情况，折腾了好久最终通过比较 `iView` 官方的「变量覆盖」教程和 `Vue CLI` 官方的自动导入教程发现了问题所在：

## 发现错误

`iView` 推荐的「变量覆盖」方法是这样的：

![变量覆盖][2]

实质上就是新建一个 `less` 文件，引入 `iview` 的 `less` 入口文件，覆盖官方提供的可覆盖变量，然后在 `mian.js` 中引用自定义的 `less` 文件代替引用 `iview less` 入口文件。

而 `Vue CLI` 提供的自动化导入教程是这样的：

![自动化导入][3]

实质上就是在每个文件（全局）导入我们提供的样式文件（示例中是 `./src/styles/imports.styl'` 文件）

正常来说按照 `Vue CLI` 官方给出的自动化导入教程配置就可以在开发环境下为全局导入「基础样式库」，但我因为先使用了 `iView` 官方提供的「变量覆盖」方法修改了基础样式，又把带有引入 `iview less` 入口文件命令的自定义 `less` 文件当成基础样式库导入到了全局，这就造成了全局每一个页面都导入了一次 `iview` 样式从而引发前面所说的 `CSS` 样式重复载入。

我当时的错误配置如下：

![错误配置-1](https://img.blanc.site//wiki/img/20190827184116.png)

![错误配置-2](https://img.blanc.site//wiki/img/20190827184120.png)

## 解决方案

正确的配置方法是将原本糅杂的样式文件分离成「全局样式入口文件」和「基础样式库文件」两个文件：

「全局样式入口文件」用来引入需要的文件（在我的项目中是「`iview less` 入口文件」和「基础样式库文件」）;
「基础样式库文件」用来存放项目的基础样式（一般为 `less sass` 变量和公用函数等等）;

修改后的配置如下：

![正确配置-1](https://img.blanc.site//wiki/img/20190827184124.png)

![正确配置-2](https://img.blanc.site//wiki/img/20190827184127.png)

![正确配置-3](https://img.blanc.site//wiki/img/20190827184132.png)

![正确配置-4](https://img.blanc.site//wiki/img/20190827184135.png)

注：我的「自动化导入」部分是参考 [码路芽子-Vue Cli3.0 全局引入 less 变量][4] 进行设置的。

效果如下：

![最终效果](https://img.blanc.site//wiki/img/20190827185053.png)

## 参考

1. [码路芽子-Vue Cli3.0 全局引入 less 变量][4]
2. [Vue CLI 官方文档-CSS 相关-自动化导入][5]


[1]:https://img.blanc.site//wiki/img/20190827171105.png
[2]:https://img.blanc.site//wiki/img/20190827172101.png
[3]:https://img.blanc.site//wiki/img/20190827172323.png
[4]:https://juejin.im/post/5cdb71d6f265da03b2045333
[5]:https://cli.vuejs.org/zh/guide/css.html#%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AF%BC%E5%85%A5