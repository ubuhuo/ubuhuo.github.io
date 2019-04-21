---
title: ZooKeeper 学习笔记：一
date: 2019/4/2
categories:
  - 软甲使用
  - ZooKeeper
tags:
  - ZooKeeper 学习笔记
copyright: true
---

ZooKeeper 的设计目标是将那些复杂且容易出错的分布式一致性服务封装起来，构成一个高效可靠的原语集，并以一系列简单易用的接口提供给用户使用。

> 原语： 操作系统或计算机网络用语范畴。是由若干条指令组成的，用于完成一定功能的一个过程。具有不可分割性·即原语的执行必须是连续的，在执行过程中不允许被中断。

ZooKeeper 是一个典型的分布式数据一致性解决方案，分布式应用程序可以基于 ZooKeeper 实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master 选举、分布式锁和分布式队列等功能。

Zookeeper 一个最常用的使用场景就是用于担任服务生产者和服务消费者的注册中心。 

## zoo.cfg 配置参数

* tickTime
通信心跳数，Zookeeper 服务器与客户端心跳时间，单位毫秒。
它用于心跳机制，并且设置最小的 session 超时时间为两倍心跳时间。

* initLimit
集群中的 Follower（跟随者）服务器与 Leader（领导者）服务器之间初始连接时能容忍的最多心跳数（总时间就是它乘以 tickTime），用它来限定集群中的 Zookeeper 服务器连接到 Leader 的时限。

* syncLimit
同步通信时限，集群中 Leader 与 Follower 之间的最大响应时间单位，假如响应超过 syncLimit * tickTime，Leader 认为 Follwer 死掉，从服务器列表中删除 Follwer。

* dataDir
必须配置

* dataLogDir
日志目录，如果不配置和 dataDir 同用一个目录

* clientPort
客户端端口，默认 2181

## ZooKeeper 基本数据模型

树形结构，类似 UNIX 目录结构

每一个节点都称之为 znode，它也可以有子节点也可以有数据

每个节点可以分为临时节点和永久节点「持久化」，临时节点在客户端断开后消失

每个 zk 节点都有各自的版本号，可以通过命令行来显示节点信息

每当节点数据发生变化，那么该节点的版本号会累加（乐观锁）

删除/修改过时节点时，版本号不匹配会报错

每个 zk 节点存储的数据不宜过大的，n * k 即可

节点可以设置权限 acl「权限控制列表」，可以通过权限来限制用户访问

## ZooKeeper 数据模型基础操作

### 客户端连接

### 查看 znode 结构

### 关闭客户端连接

## 应用场景

master 节点选举，主节点挂了之后，从节点会接手工作，并且保证主节点是唯一的，这也是所谓的首脑模式，从而保证集群的高可用。

统一配置文件管理，即只需部署一台服务器，则可以把相同的配置文件同步更新到其他所有服务器，此操作在云计算中用的特别多。

发布与订阅，类似消息队列 MQ(amq, rmq...)，dubbo 发布者把数据存在 znode 上，订阅者会读取这个数据。

提供分布式锁，分布式环境中不同进程之间争夺资源，类似多线程中的锁。

集群管理，集群中保证数据的强一致性

## zk 常用命令操作

### ls 与 ls2

### get 与 stats

### create

### set

### delete

## zk 特性 - session 的基本原理

客户端与服务端之间的连接存在会话

每个会话都可以设置一个超时时间

心跳结束，session 过期

session 过期，则临时节点 znode 会被抛弃

心跳机制：客户端想服务端的 ping 包请求

## zk 特性 - watcher 机制

针对每个节点的操作，都会有一个监督者 watcher

当监控的某个对象（znode）发生了变化，则触发 watcher 时间

zk 中的 watcher 是一次性的，触发后立即销毁

父节点，子节点的增删改都能触发其 watcher

针对不同类型的操作，触发的 watcher 事件也不同：
1. （子）节点创建事件
2. （子）节点删除事件
3. （子）节点数据变化事件

### 设置 watcher

通过 get path [watch] 设置 watcher

### watcher 事件类型

[stat watch]创建父节点：NodeCreated

[get watch]修改父节点：NodeDataChanged

[get watch]删除父节点：NodeDeleted

[ls watch]创建子节点：NodeChildrenChanged

[ls watch]删除子节点：NodeChildrenChanged

### watcher 使用场景

统一资配置 

## ACL(access control lists) 权限控制

针对节点可以设置相关读写等权限，目的是为了保障数据安全性

权限 permissions 可以指定不同的权限范围以及角色



## ACL 命令行

getAcl：获取某个节点的 acl 权限信息
setAcl：设置某个节点的 acl 权限信息
addauth：输入认证授权信息，注册时输入铭文密码（登录）但是在 zk 系统里，密码是以加密形式存在的

## ACL 的构成

zk 的 acl 通过 scheme:id:permissions 来构成权限列表
scheme：代表采用的某种权限机制
id：代表允许访问的用户
permissions：权限组合字符串

### scheme

world：world 下只有一个 id，即只有一个用户也就是 anyone，写法：world:anyone:[permissions]

auth：代表认证登录，需要注册用户有权限，形式：auth:user:password:[permissions]

digest：需要对密码进行加密才能访问，行为digest:username:BASE64(SHA1(password)):[permissions]

PS: auth 是明文密码，digest 是密文密码

ip：设置 ip 指定的 ip 地址，比如 ip:192.168.1.1:[permissions]

super：代表超级管理员，拥有所有权限（需要设置文件）

### permissions

权限字符串缩写 crdwa

CREATE：在当前节点下创建子节点
READ：获取节点、子节点信息
WRITE：设置节点数据
DELETE：删除子节点
ADMIN：设置权限（有没有权限设置权限）

## zk 四字命令 Four Letter Words

zk 可以通过自身提供的简写命令来和服务器进行交互

需要使用 nc 命令

echo [commond] | nc [ip] [prot]

## zookeeper 集群搭建

zk 集群，主从节点，心跳机制（选举模式）



参考：

[「冰封承諾-体验ZooKeeper」][1]

[1]: https://bfchengnuo.com/2019/02/26/%E4%BD%93%E9%AA%8CZooKeeper/