---
title: MySQL GRANT 授权语句
date: 2019/9/26
categories:
  - 数据库
  - MySQL
tags:
  - MySQL GRANT
copyright: true
---

在服务器创建完 `MySQL` 数据库后经常需要给角色分配远程连接的权限，一般会在初始化的时候选择禁用远程连接，然后再通过 `GRANT` 给固定 `IP` 开启远程连接数据库的权限。

## GRANT 语句基本语法

```mysql
GRANT privilege[,privilege,...] ON database.table 
-> TO username@user_ip [IDENTIFIED BY password_you_want]
-> [REQUIRE tsl_option] [WITH [GRANT_OPTION] resource_option]];
```

上面为一整句 `GRANT` 语句，为了方便阅读我做了分行处理。

第一行为权限分配：

`GRANT` 关键字后紧跟着需要分配的权限，其中 `privilege` 有很多可选项（后面会单独讲），也可以设置为 `ALL` 表示给予全部权限；

`ON` 关键字后紧跟着针对哪个数据库和数据表，其中 `database` 可以为任意数据库名，也可以使用通配符 `*` 表示全选；`table` 可以为任意数据表名，同样可以使用通配符 `*` 表示全选，因此 `*.*` 则表示全部数据库的全部表；

第二行为用户指定：

`TO` 关键字后指定权限分配指向的 `MySQL` 用户和 `IP` 地址，两者使用 `@` 隔开，其中 `username` 可以已经是已经存在的用户也可以是还不存在的用户（该命令会自动创建所需用户），也可以使用通配符 `%` 表示全选；设置 `IP` 是可以在任意 `IPV4` 的 `IP` 段使用通配符 `%` 表示全选，例如 `192.168.0.%`，也可以直接设置为 `%` 表示全选；

`INDENTIFIED BY` 为可选字段，后面跟随想要设置的用户登录密码

第三行为详细配置项：

`REQUIRE` 为可选项，用来设置数据库安全连接；

`WITH GRANT OPTION` 为可选项，用来做一些附加设置，这里就不详细展开了；

## MySQL GRANT 语句示例

```mysql
# 赋予通过 localhost 登录的 root 用户全部数据库和数据表的全部权限
GRANT ALL ON *.* TO 'root'@'loaclhost';

# 赋予通过任意 ip 登录的 test_user 用户查看 testdb 数据库下全部数据表的权限
GRANT select ON testdb.* to 'test_user'@'%';
```

## GRANT 相关命令

```mysql
# 查看除了 ALL 以外其他可选择的 privilege 选项
SHOW privileges;

# 查看已经分配给某个特定用户的 GRANT 语句
SHOW GRANTS FOR username@user_ip
```

## 参考

[易百教程-MySQL授予权限（Grant语句）][1]
[简书-Rick_Ji-mysql的grant权限][2]

[1]: https://www.yiibai.com/mysql/grant.html
[2]: https://www.jianshu.com/p/fd1cb8657702