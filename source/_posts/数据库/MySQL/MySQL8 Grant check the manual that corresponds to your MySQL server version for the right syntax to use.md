---
title: MySQL8 Grant check the manual that corresponds to your MySQL server version for the right syntax to use.m
date: 2020/08/26
categories:
  - 数据库
  - MySQL
tags:
  - MySQL GRANT
copyright: true
---

## 错误重现：

`OS`: `Windows 10`
`MySQL version`: `8.0.21`

PS：我已经将 `MySQL` 的加密规则修改为 `mysql_native_password`

```mysql
grant all on *.* to 'root'@'%' identified by 'testpassword';
```

```mysql
1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'IDENTIFIED BY 'testpassword' at line 1
```

## 问题解决

最开始判断为加密规则的问题后续检查后发现之前已经就该过加密规则了，因此排除这一错误原因。谷歌搜索后找到了 [一篇文章][1] 给出了解决方案：

```mysql
create user 'root'@'%' identified by 'testpassword';

grant all on *.* to 'root'@'%';
```

根据文章给出的解释：似乎是 `MySQL` 放弃了对这个简写版的支持，现在需要一个稍长一点的版本来代替。

## 参考

1. [@mattiasgeniar-MySQL 8 removes shorthand for creating user + permissions][1]

[1]: https://ma.ttias.be/mysql-8-removes-shorthand-creating-user-permissions/