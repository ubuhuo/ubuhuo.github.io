---
title: Vue 使用 vue-cli 搭建项目
date: 2018/5/27
categories:
  - 编程框架
  - Vue
tags:
  - Vue 使用 vue-cli 搭建项目
copyright: true
abbrlink: 5cce5216
---

## 1. 安装 node.js

## 2. 安装 vue-cli

```powershell
npm install -g vue-cli
```

若速度慢可以采用国内淘宝镜像安装

最新的 `Vue` 项目模板中，都带有 `webpack` 插件，所以这里可以不安装 `webpack`

安装后通过 `vue -V` 检查是否安装成功

## 3. 在自己的目录下生成项目

```powershell
vue init webpack Vue-Project
```

其中 `webpack` 是模板名称，可以到 [Vue.js 的 GitHub][1] 上查看更多的模板

`Vue-Project` 是自定义的项目名称，命令执行之后，会在当前目录生成一个以该名称命名的项目文件夹

配置完成后，可以看到目录下多出了一个项目文件夹，里面就是 `vue-cli` 创建的一个基于 `webpack` 的 `Vue.js` 项目

然后进入项目目录（`cd Vue-Project`），使用 `cnpm` 安装依赖

```
cnpm install
```

然后启动项目

```
npm run dev
```

如果浏览器打开之后，没有加载出页面，有可能是本地的 `8080` 端口被占用，需要修改一下配置文件 `config>index.js`

建议将端口号改为不常用的端口。另外我还将 `build` 的路径前缀修改为 `./`（原本为 `/` ），是因为打包之后，外部引入 `js` 和 `css` 文件时，如果路径以 `/` 开头，在本地是无法找到对应文件的（服务器上没问题）。所以如果需要在本地打开打包后的文件，就得修改文件路径。

## 4. 打包上线

自己的项目文件都需要放到 `src` 文件夹下

项目开发完成之后，可以输入 `npm run build` 来进行打包工作

```
npm run build
```

打包完成后，会生成 `dist` 文件夹，如果已经修改了文件路径，可以直接打开本地文件查看

项目上线时，只需要将 `dist` 文件夹放到服务器就行了。

[1]: https://github.com/vuejs-templates