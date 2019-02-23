---
title: 「转载」一劳永逸的搞定 flex 布局
date: 2018/6/30
categories:
  - 编程语言
  - CSS
tags:
  - 一劳永逸的搞定 flex 布局
copyright: true
abbrlink: 2db90137
---


> 作者：osimly
> 链接：https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb
> 来源：掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 寻根溯源话布局

一切都始于这样一个问题：怎样通过 `CSS` 简单而优雅的实现水平、垂直同时居中。记得刚开始学习 `CSS` 的时候，看到 `float` 属性不由得感觉眼前一亮，顺理成章的联想到 `Word` 文档排版中用到的的左对齐、右对齐和居中对齐，然而很快就失望的发现 `CSS` 中并不存在 `float: center` 的写法，那么 `text-align: center`、`verticle-align: center` 是否可行呢？答案也是否定的。这两个属性只能用于行内元素，对于块级元素的布局是无效的。
在网页布局没有进入 `CSS` 的时代，排版几乎是通过 `table` 元素实现的，在 `table` 的单元格里可以方便的使用 `align`、`valign` 来实现水平和垂直方向的对齐，随着 `Web` 语义化的流行，这些写法逐渐淡出了视野，`CSS` 标准为我们提供了 `3` 种布局方式：标准文档流、浮动布局和定位布局。这几种方式的搭配使用可以轻松搞定 `PC` 端页面的常见需求，比如实现水平居中可以使用 `margin: 0 auto`，实现水平垂直同时居中可以如下设置：

```css
.dad {
    position: relative;
}
```
```css
.son {
    position: absolute;
    margin: auto;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
}
```
---
```css
.dad {
    position: relative;
}
```
```css
.son {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -50px;
    margin-left: -50px;
}
```

然而，这些写法都存在一些缺陷：缺少语义并且不够灵活。我们需要的是通过 1 个属性就能优雅的实现子元素居中或均匀分布，甚至可以随着窗口缩放自动适应。在这样的需求下，`CSS` 的第 4 种布局方式诞生了，这就是我们今天要重点介绍的 `flex` 布局。

## flex 基本概念

使用 `flex` 布局首先要设置父容器 `display: flex`，然后再设置 `justify-content: center` 实现水平居中，最后设置 `align-items: center` 实现垂直居中。

```css
#dad {
    display: flex;
    justify-content: center;
    align-items: center
}
```
![示例1][1]

就是这么简单，大功告成。等等，好像哪里不对，`justify-content` 和 `align-items` 是啥？哪里可以看出横向、竖向的语义？是的，`flex` 的确没有那么简单，这就要从两个基本概念说起了。
![解析1][2]

说来也不难，`flex` 的核心的概念就是 **容器** 和 **轴**。容器包括外层的 **父容器** 和内层的 **子容器**，轴包括 **主轴** 和 **交叉轴**，可以说 `flex` 布局的全部特性都构建在这两个概念上。`flex` 布局涉及到 12 个 `CSS` 属性（不含 `display: flex`），其中父容器、子容器各 6 个。不过常用的属性只有 4 个，父容器、子容器各 2 个，我们就先从常用的说起吧。

### 1. 容器

> 容器具有这样的特点：父容器可以统一设置子容器的排列方式，子容器也可以单独设置自身的排列方式，如果两者同时设置，以子容器的设置为准。

![解析2][3]

#### 1.1 父容器

* 设置子容器沿主轴排列：justify-content

`justify-content` 属性用于定义如何沿着主轴方向排列子容器。

![解析3][4]

> flex-start：起始端对齐

![示例2][5]

> flex-end：末尾段对齐

![示例3][6]

> center：居中对齐

![示例4][7]

> space-around：子容器沿主轴均匀分布，位于首尾两端的子容器到父容器的距离是子容器间距的一半。

![示例5][8]

> space-between：子容器沿主轴均匀分布，位于首尾两端的子容器与父容器相切。

![示例6][9]

* 设置子容器如何沿交叉轴排列：`align-items`

`align-items` 属性用于定义如何沿着交叉轴方向分配子容器的间距。

![解析4][10]

> flex-start：起始端对齐

![示例7][11]

> flex-end：末尾段对齐

![示例8][12]

> center：居中对齐

![示例9][13]

>baseline：基线对齐，这里的 baseline 默认是指首行文字，即 first baseline，所有子容器向基线对齐，交叉轴起点到元素基线距离最大的子容器将会与交叉轴起始端相切以确定基线。

![示例10][14]

> stretch：子容器沿交叉轴方向的尺寸拉伸至与父容器一致。

![示例11][15]

#### 1.2 子容器

* 在主轴上如何伸缩：**flex**

![示例12][16]

子容器是有弹性的（`flex` 即弹性），它们会自动填充剩余空间，子容器的伸缩比例由 `flex` 属性确定。

`flex` 的值可以是无单位数字（如：1, 2, 3），也可以是有单位数字（如：15px，30px，60px），还可以是 `none` 关键字。子容器会按照 `flex` 定义的尺寸比例自动伸缩，如果取值为 none 则不伸缩。

虽然 `flex` 是多个属性的缩写，允许 1 - 3 个值连用，但通常用 1 个值就可以满足需求，它的全部写法可参考下图。

![解析5][17]

* 单独设置子容器如何沿交叉轴排列：**align-self**

![解析6][18]

每个子容器也可以单独定义沿交叉轴排列的方式，此属性的可选值与父容器 `align-items` 属性完全一致，如果两者同时设置则以子容器的 `align-self` 属性为准。

> flex-start：起始端对齐

![示例13][19]

> flex-end：末尾段对齐

![示例14][20]

> center：居中对齐

![示例15][21]

> baseline：基线对齐

![示例16][22]

> stretch：拉伸对齐

![示例17][23]

### 2. 轴

如图所示，**轴** 包括 **主轴** 和 **交叉轴**，我们知道 `justify-content` 属性决定子容器沿主轴的排列方式，`align-items` 属性决定子容器沿着交叉轴的排列方式。那么轴本身又是怎样确定的呢？在 `flex` 布局中，`flex-direction` 属性决定主轴的方向，交叉轴的方向由主轴确定。

![解析7][24]

* 主轴

主轴的起始端由 `flex-start` 表示，末尾段由 `flex-end` 表示。不同的主轴方向对应的起始端、末尾段的位置也不相同。

> 向右：flex-direction: row

![示例18][25]

> 向下：flex-direction: column

![示例19][26]

> 向左：flex-direction: row-reverse

![示例20][27]

> 向上：flex-direction: column-reverse

![示例21][28]

* 交叉轴

主轴沿逆时针方向旋转 90° 就得到了交叉轴，交叉轴的起始端和末尾段也由 `flex-start` 和 `flex-end` 表示。

上面介绍的几项属性是 `flex` 布局中最常用到的部分，一般来说可以满足大多数需求，如果实现复杂的布局还需要深入了解更多的属性。

---

## flex 进阶概念

### 1. 父容器

* 设置换行方式：**flex-wrap**

决定子容器是否换行排列，不但可以顺序换行而且支持逆序换行。

![解析8][29]

> nowrap：不换行

![示例22][30]

> wrap：换行

![示例23][31]

> wrap-reverse：逆序换行

逆序换行是指沿着交叉轴的反方向换行。

![示例24][32]

* 轴向与换行组合设置：**flex-flow**

`flow` 即流向，也就是子容器沿着哪个方向流动，流动到终点是否允许换行，比如 `flex-flow: row wrap`，`flex-flow` 是一个复合属性，相当于 `flex-direction` 与 `flex-wrap` 的组合，可选的取值如下：

* `row`、`column` 等，可单独设置主轴方向

* `wrap`、`nowrap` 等，可单独设置换行方式

* `row nowrap`、`column wrap` 等，也可两者同时设置



* 多行沿交叉轴对齐：**align-content**

当子容器多行排列时，设置行与行之间的对齐方式。

![解析9][33]

> flex-start：起始端对齐

![示例25][34]

> flex-end：末尾段对齐

![示例26][35]

> center：居中对齐

![示例27][36]

> space-around：等边距均匀分布

![示例28][37]

> space-between：等间距均匀分布

![示例29][38]

> stretch：拉伸对齐

![示例30][39]

### 2. 子容器

* 设置基准大小：**flex-basis**

`flex-basis` 表示在不伸缩的情况下子容器的原始尺寸。主轴为横向时代表宽度，主轴为纵向时代表高度。

![示例31][40]

![示例32][41]

* 设置扩展比例：**flex-grow**

子容器弹性伸展的比例。如图，剩余空间按 1:2 的比例分配给子容器。

![示例33][42]

![示例34][43]

* 设置收缩比例：**flex-shrink**

子容器弹性收缩的比例。如图，超出的部分按 1:2 的比例从给子容器中减去。

![示例35][44]

![示例36][45]

* 设置排列顺序：**order**

改变子容器的排列顺序，覆盖 `HTML` 代码中的顺序，默认值为 0，可以为负值，数值越小排列越靠前。

![示例37][46]

---

以上就是 flex 布局的全部属性，一共 12 个，父容器、子容器各 6 个，可以随时通过下图进行回顾。

![解析10][47]

> 参考资料：
> [MDN: CSS Flexible Box Layout][48]
> [Flex 布局教程：语法篇][49]
> [Flex 布局学习笔记][50]
> [30 分钟学会 Flex 布局][51]
> [弹性盒模型Flex指南][52]

[1]:https://www.blanc.site/wp-content/uploads/2018/07/2018070904420670.png
[2]:https://www.blanc.site/wp-content/uploads/2018/07/2018070904512729.png
[3]:https://www.blanc.site/wp-content/uploads/2018/07/2018070904553180.png
[4]:https://www.blanc.site/wp-content/uploads/2018/07/2018070904565257.png
[5]:https://www.blanc.site/wp-content/uploads/2018/07/2018070904580243.png
[6]:https://www.blanc.site/wp-content/uploads/2018/07/2018070904583674.png
[7]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905014130.png
[8]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905020317.png
[9]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905021732.png
[10]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905025410.png
[11]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905030621.png
[12]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905032298.png
[13]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905034038.png
[14]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905042813.png
[15]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905044162.png
[16]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905055737.png
[17]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905060768.png
[18]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905061561.png
[19]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905062962.png
[20]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905065070.png
[21]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905070460.png
[22]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905072546.png
[23]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905083051.png
[24]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905084576.png
[25]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905090051.png
[26]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905091172.png
[27]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905091920.png
[28]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905093792.png
[29]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905094326.png
[30]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905095959.png
[31]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905102769.png
[32]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905103652.png
[33]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905104696.png
[34]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905105886.png
[35]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905111291.png
[36]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905112013.png
[37]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905113560.png
[38]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905114310.png
[39]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905115050.png
[40]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905123830.png
[41]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905124610.png
[42]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905125326.png
[43]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905130121.png
[44]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905131169.png
[45]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905131834.png
[46]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905132636.png
[47]:https://www.blanc.site/wp-content/uploads/2018/07/2018070905133474.png
[48]:https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FCSS%2FCSS_Flexible_Box_Layout
[49]:https://link.juejin.im/?target=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2015%2F07%2Fflex-grammar.html
[50]:https://link.juejin.im/?target=https%3A%2F%2Fbuzheng.org%2F2017%2F20170119-flex-layout-note.html
[51]:https://link.juejin.im/?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F25303493
[52]:https://link.juejin.im/?target=http%3A%2F%2Flouiszhai.github.io%2F2017%2F01%2F13%2Fflex%2F