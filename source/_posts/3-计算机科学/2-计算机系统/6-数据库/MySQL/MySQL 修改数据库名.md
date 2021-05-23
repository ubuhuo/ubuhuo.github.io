---
title: MySQL 修改数据库名
date: 2019/1/14
categories:
  - 3-计算机科学
  - 2-计算机系统
  - 6-数据库
  - MySQL
tags:
  - MySQL
copyright: true
---

首先，MySQL 没有直接更改数据库名称的命令；
其次，无论使用什么方法都要记得提前备份数据库。

注：`RENAME DATABASE` 这条命令在 MySQL 5.1.7 中被加入，但很快就被发现其所带来的危险，于是在 MySQL 5.1.23 中这条命令被移除。

## 推荐方案

假如现在我们想把数据库名由 ABC 更改为 EFG。

我们可以在数据库外执行以下命令：

```powershell
mysqladmin -u root -p create EFG
mysqldump ABC | mysql -u root -p EFG
```

当你确定原数据库中的数据都被复制到了新数据库中，就可以把原数据库删掉了。

```powershell
drop database ABC
```

缺点：当数据库较大的时候，这种方法耗时耗力

## 其他方案（来自网络，不确保其安全性）

### 重命名数据库里面的所有表

`information_schema` 数据库 `table` 表记录了数据库中所有表的信息

```sql
CREATE DATABASE new_db_name; # 创建新的数据库
RENAME TABLE db_name.table1 TO new_db_name.table1; # 对所有的表进行重命名
DROP DATABASE db_name; # 删除原来的数据库
```

当数据库表包含表很多的情况下，这样操作是比较效率也比较低，可以通过以下脚本进行批量修改:

生成 SQL 脚本

```powershell
mysql -uroot -p -e "select concat('rename table db.',table_name,' to new_db.',table_name,';') from information_schema.TABLES where TABLE_SCHEMA='db';" > rename_mysql_name.sql
```

执行 SQL 语句

```powershell
mysql -uroot -p < rename_mysql_name.sql # 批量进行修改
```