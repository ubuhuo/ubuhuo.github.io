---
title: vue-cli 下配置 Element + axios + Mockjs
date: 2018/5/31
categories:
  - 编程框架
  - Vue
tags:
  - vue-cli 下配置 Element + axios + Mockjs
copyright: true
abbrlink: b4f7e05e
---

近期打算配合学习计划写一个小的匿名树洞，打算结合之前学习的 `ThinkPHP5` 和正在学习的 `Vue` 两个框架，最近在写项目的前端部分踩了好多坑还在一步一步摸索中。

本文是讲解在刚开始接触 `Vue` 时就使用 `vue-cli` 脚手架（官方不建议这么做而且确实有道理）的情况下如何配置 `Element + axios + Mockjs` 完成前后端分离开发（听起来很吊的样子）。

其中：
`Element` 为饿了吗团队编写的一套 `Vue` 组件库
`axios` 是一个基于 `promise` 的 `HTTP` 库，可以用在浏览器和 `node.js` 中(简单的说就是一个 `Ajax` 构造器）
`Mockjs` 是一个大佬写的[生成随机数据，拦截 `Ajax` 请求]的脚本（我其实还没完全研究明白这个东西怎么用）

## 1. 安装 `vue-cli` 脚手架

参考博文：[Vue 使用 vue-cli 搭建项目][1]

## 2. 安装并配置 Element

### 2.1 安装 loader 模块

```powershell
npm install style-loader --save-D
npm install css-loader --save-D
npm install file-loader --save-D
```

### 2.2 安装 Element-UI 模块

```
npm install element-ui -S
```

### 2.3 在项目 main.js 中引入

```javascript
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-default/index.css'
Vue.use(ElementUI)
```

若安装后报错尝试重新安装 `loader` 模块

以上就成功在 `Vue` 脚手架上安装了 `Element` 组件，可以按照 [Element 官网][2] 给出的简例进行测试。

## 3. 安装并配置 axios

### 3.1 安装 `axios`

```powershell
npm install axios --save-dev
```

安装其他插件的时候，可以直接在 `main.js` 中引入并 `Vue.use()`，但是 `axios` 并不能 `use` ，只能每个需要发送请求的组件中即时引入。

为了解决这个问题，有两种开发思路，一是在引入 `axios` 之后，修改原型链，二是结合 `Vuex`，封装一个 `aciton`。这里只说修改原型链的方式。

### 3.2 如何改写原型链

在 `main.js` 中引入 `axios`

```javascript
import axios from 'axios'
```

这时候如果在其它的组件中，是无法使用 `axios` 命令的。所以我们将 `axios` 改写为 `Vue` 的原型属性。

```javascript
Vue.prototype.$http= axios
```

在 `main.js` 中添加了这两行代码之后，就能直接在组件的 `methods` 中使用 `$http` 命令，例如：

```javascript
methods: {
  show() {
    this.$http({
      method: 'get',
      url: '/user',
      data: {
        name: 'virus'
      }
   })
}
```

## 4. 安装并配置 MockJs

`MockJs` 的使用教程网上很多但是大都是基于低版本 `Vue` 做得现在已经不适用了，于是我结合了 `MockJs` 官方维基和一些大佬们写的最新版配置过程自己作了一次尝试目前在我自己的开发环境已经测试可用，若你们在配置的过程中出现问题欢迎随时留言我会在看到后尽快回复。

### 4.1 安装 MockJs

```powershell
npm install mockjs --save-dev
```

### 4.2 配置 MockJs 入口文件

在项目 `src` 目录下创建 `mock` 文件夹，定义 `MockJs` 主文件 `mock.js`，在该文件中定义拦截路由配置：

![设置截图][3]

![设置截图][4]

### 4.3 在 main.js 中引入 MockJs

![设置截图][5]

### 4.4 成果测试

因为我在安装 `MockJS` 之前就已经写了一些代码了所以我就在原来的代码基础上加了一个测试的 `button` 按钮以下是代码截图和前端展示：

![代码截图][6]
![代码截图][7]
![成果展示][8]

到此此次教程结束，中途因为考试而终止了一次编写所以显得有些仓促，如果你在配置的过程中出现了问题欢迎留言。

[1]: https://wiki.blanc.site
[2]: http://element.eleme.io/#/zh-CN/component/button
[3]: https://img.blanc.site/wiki/img/14.png
[4]: https://img.blanc.site/wiki/img/15.png
[5]: https://img.blanc.site/wiki/img/16.png
[6]: https://img.blanc.site/wiki/img/17.png
[7]: https://img.blanc.site/wiki/img/18.png
[8]: https://img.blanc.site/wiki/img/19.png