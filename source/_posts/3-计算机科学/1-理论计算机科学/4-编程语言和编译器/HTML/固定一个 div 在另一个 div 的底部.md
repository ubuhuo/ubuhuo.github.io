---
title: 固定一个 div 在另一个 div 的底部
date: 2018/1/27
categories:
  - 3-计算机科学
  - 1-理论计算机科学
  - 4-编程语言和编译器
  - HTML
copyright: true
---

```html
<html>
<head>
  <style>

    .big{
            width:20%;
            height:100%;
            float:left;
            position:relative;
          }

    .small{
            width:100%;
            position:absolute;
            bottom:0;
            }
    </style>
</head>
<body>
    <div class="big">
        <div class="small">我在底部</div>
    </div>
</body>
</html>
```

如果要始终固定在页面底部，那么就需要使用：`position：fixed`