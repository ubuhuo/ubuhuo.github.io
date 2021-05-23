---
title: 利用 position + rgba 将 HTML 背景灰化
date: 2018/1/20
categories:
  - 3-计算机科学
  - 1-理论计算机科学
  - 4-编程语言和编译器
  - HTML
copyright: true
---

最近正在摸索着写自己的第一个主题，在 `Copy` 大佬页面设计的时候发现虽然用了同一张图做背景我 `Copy` 出来的页面却显得很不协调：

![大佬博客][1]

![临摹作品][2]

很明显我临摹的作品背景偏亮，使得副标题显示的很模糊，因此我断定是我临摹过程中漏掉了一个步骤[然而具体是什么步骤就不得而知了]，经过我一顿百度摸索发现可以在背景上面再加一层纯色蒙版然后调整蒙版不透明度即可：

代码示例：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>ceshi</title>
        <style type="text/css">
            .top {
                /*根据需求设置宽和高*/
                width: 500px;
                height: 500px;
                /*相对定位*/
                position: relative;
                /*利用 rgba 设置背景色 前三个数字决定颜色，最后一个数字代表不透明度，1为完全不透明*/
                background-color: rgba(19,18,16,0.5);
            }
            .down {
                /*设置背景图片*/
                background-image: url(img/banner.png);
                /*设置绝对定位*/
                position: absolute;
                top: 0px;
                bottom: 0;
                left: 0;
                right: 0;
                /*将背景置于底层*/
                z-index: -1;
                /*自适应分辨率*/
                background-position: center center;
                background-size: cover;
            }
        </style>
    </head>
    <body>
        <div class="top">
        /*蒙版区*/
        </div>

        <div class="down">
        /*背景图存放区*/
        </div>
    </body>
</html>
```

[1]: https://img.blanc.site/wiki/img/22.png
[2]: https://img.blanc.site/wiki/img/21.png