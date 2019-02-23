---
title: 如何用纯 HTML 区分一个 form 表单中的多个 submit
date: 2017/7/27
categories:
  - 编程语言
  - HTML
tags:
  - 纯 HTML 区分 form 表单中多个 submit
copyright: true
abbrlink: cd7f373a
---

当然可以用 `JS` 的方法，但是对于初学者来说 `JS` 函数有些麻烦，在这里我分享两个纯 `HTML` 区分一个 `form` 中不同 `submit` 的方法：

## 1. 使用不同的 name 属性

### 1.1 前端代码：

```html
<form method="post">
<input type="submit" name="save" value="保存设置"/>
<input type="submit" name="reset" value="复位设置"/>
</form>
```

### 1.2 后台验证：

```php
// PHP 代码
if ( $_REQUEST['save'] ) {
// ...
} elseif ( $_REQUEST['reset'] ) {
// ...
}
```

原理：只有被点击的 `submit` 按钮数据才会被提交

## 2. 使用相同的 name，不同的 value

### 2.1 前端代码：

```html
<form method="post">
<input type="submit" name="action" value="保存设置"/>
<input type="submit" name="action" value="复位设置"/>
</form>
```

### 2.2 后台验证：

```php
if ( $_REQUEST['action']=='保存设置' ) {
// ...
} elseif ( $_REQUEST['action']=='复位设置' ) {
// ...
}
```

要注意的是方法二中，由于用到了中文，前后台编码要保持一致，否则会导致判断失败。