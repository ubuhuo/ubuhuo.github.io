---
title: 「转载」解析谷歌 Chrome 各分支版本
date: 2017/8/17
categories:
  - 2-软件使用
  - Chrome
tags:
  - 转载
  - Chrome
copyright: true
---

> 文章内容来自 [cnBeta：解析谷歌Chrome各分支版本][1]

网友尝试下载使用 Chrome 时，才发现 Chromium 版本众多，版本号更是一周升级一次，那些 Chromium、Dev、Beta、Stable 等名词让人很难选择到底自己适合使用哪一款。

通常来说，最早释出的是 Chromium，经过功能改进和 Bug 修复后可依次上升为 Chrome Dev、Beta，最终定格为 Chrome Stable 稳定版。

以 Chromium 5.0.375.9 为例，5.0 是指新一代浏览器版本；375 是当前代码号，推出新功能；9 则侧重于安全补丁和修复稳定性，一般不包括功能更新；

## 各种版本的特点：

### Chromium：

Chromium 是谷歌浏览器开源项目，新功能会率先在 Chromium 上实现，待验证后才会应用在 Chrome 上，故 Chrome 的功能会相对落后但较稳定（实际上稳定性也差不多）；

Chromium 的更新速度很快，每隔数小时即有新的开发版本。

### Chrome dev：

基于最新的 Chromium Build，经常每周就更新释出的功能；
与 Beta 版十分相似，但稳定性较差，不适合公共使用；

### Chrome beta：

基于 Chrome dev；
按月更新；
崩溃等重大故障较少发生，功能比 dev 更加完善

### Chrome stable：

基于最新的测试版本，已知 Bug 均被修复，功能发挥稳定；
按季度更新；
稳定性有很大提升，适用于各个环境；

## Chromium 与 Chrome 的差异主要表现在以下方面：

程式图示：两者图示只在色彩上有不同，Chromium 的是天蓝色系，而 Chrome 的则是 Google 公司的代表色。

标题栏：Chromium 的标题栏无任何字样，而 Chrome 的标题栏在右侧有白色的 Google 字样。

自动更新：Chromium 不开放自动更新功能，所以无法自动更新，而 Chrome 则可自动连上 Google 的伺服器更新，但新版的推出很慢。

功能差异：如前所述，新功能会率先在 Chromium上 推出，Chrome 则会相对落後许多。

[1]: http://www.cnbeta.com/articles/tech/109088.htm