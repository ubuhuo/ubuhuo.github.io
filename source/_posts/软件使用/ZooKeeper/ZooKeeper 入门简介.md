---
title: ZooKeeper 入门简介
date: 2019/7/11
categories:
  - 软件使用
  - ZooKeeper
tags:
  - ZooKeeper 入门简介
copyright: true
abbrlink: f1db9330
---

## 是什么？

> Zookeeper 最早起源于雅虎研究院的一个研究小组。在当时，研究人员发现，在雅虎内部很多大型系统基本都需要依赖一个类似的系统来进行分布式协调，但是这些系统往往都存在分布式单点问题。所以，雅虎的开发人员就试图开发一个**通用的无[单点问题][1]的分布式协调框架**，以便让开发人员将精力集中在处理业务逻辑上。
> 
> 《从 Paxos 到 ZooKeeper》

`ZooKeeper` 的设计目标是将那些复杂且容易出错的分布式一致性服务封装起来，构成一个高效可靠的原语集，并以一系列简单易用的接口提供给用户使用。

`ZooKeeper` 是一个典型的分布式数据一致性解决方案，分布式应用程序可以基于 `ZooKeeper` 实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、`Master` 选举、分布式锁和分布式队列等功能。

Zookeeper 一个最常用的使用场景就是用于担任服务生产者和服务消费者的[注册中心(提供发布订阅服务)][2]。

## 概念

### 1. Session

`Session` 指的是 `ZooKeeper` 服务器与客户端会话。

在 `ZooKeeper` 中，一个客户端连接是指客户端和服务器之间的一个 `TCP` 长连接。

通过这个连接，客户端能够通过心跳检测与服务器保持有效的会话，也能够向 `Zookeeper` 服务器发送请求并接受响应，同时还能够通过该连接接收来自服务器的 `Watch` 事件通知。

在为客户端创建会话之前，服务端首先会为每个客户端都分配一个 `sessionID`。由于 `sessionID` 是 `Zookeeper` 会话的一个重要标识，许多与会话相关的运行机制都是基于 `sessionID` 的，因此，无论是哪台服务器为客户端分配的 `sessionID`，都要保证全局唯一。

### 2. Znode

在谈到分布式的时候，我们通常说的「节点」是指组成集群的每一台机器。然而，在 `Zookeeper` 中，「节点」分为两类：第一类同样是指构成集群的机器，我们称之为机器节点；第二类则是指数据模型中的数据单元，我们称之为数据节点一一znode。


### 3. 版本

`Zookeeper` 会为每个 `znode` 都维护一个 `Stat` 数据结构，`Stat` 中记录了这个 `ZNode` 的三个数据版本，分别是：`version`（当前 `znode` 的版本）、`cversion`（当前 `znode` 子节点的版本）和 `aversion`（当前 `znode` 的 `ACL` 版本）。

### 4. Watcher

`Watcher`（事件监听器），是 `Zookeeper` 中的一个重要特性。`Zookeeper` 允许用户在指定节点上注册一个或多个 `Watcher`，在一些特定事件发生时触发 `Watcher` 将事件通知到感兴趣的客户端上去，该机制是 `Zookeeper` 实现**分布式协调服务**的重要特性。

### 5. ACL

`Zookeeper` 采用 `ACL(Access Control Lists)` 策略来进行权限控制，类似于 `UNIX` 文件系统的权限控制。`Zookeeper` 定义了如下 5种 权限。

![五种权限][3]

其中尤其需要注意的是，`CREATE` 和 `DELETE` 这两种权限都是针对子节点的权限控制。

## 特性

* `ZooKeeper` 本身就是一个分布式程序（只要半数以上节点存活，`ZooKeeper` 就能正常服务）；
* 为了保证高可用，最好以集群形态来部署 `ZooKeeper`，这样只要集群中大部分机器可用，`ZooKeeper` 本身就是可用的；
* `ZooKeeper` 将数据保存在内存中，保证了高吞吐量和低延迟，但内存限制了能够存储的容量不大，此限制也是保持 `znode` 中存储的数据量较小的一个原因；
* `ZooKeeper` 是高性能的。 在「读」多于「写」的应用程序中尤其地高性能，因为「写」会导致所有的服务器间同步状态；
* `ZooKeeper` 有临时节点的概念。 只要创建临时节点的客户端会话保持活动，临时节点会一直存在，会话终结，临时节点会被删除。持久节点是指一旦某个 `znode` 被创建，除非主动进行 `znode` 的移除操作，否则这个 `znode` 将一直保存在 `Zookeeper` 上；
* `ZooKeeper` 底层其实只提供了两个功能，即：管理（存储、读取）用户程序提交的数据和为用户程序提供数据节点监听服务；


## 特点

* 顺序一致性： 从同一客户端发起的事务请求，最终将会严格地按照顺序被应用到 `ZooKeeper` 中去。
* 原子性： 所有事务请求的处理结果在整个集群中所有机器上的应用情况是一致的，也就是说，要么整个集群中所有的机器都成功应用了某一个事务，要么都没有应用。
* 单一系统映像 ： 无论客户端连到哪一个 `ZooKeeper` 服务器上，其看到的服务端数据模型都是一致的。
* 可靠性： 一旦一次更改请求被应用，更改的结果就会被持久化，直到被下一次更改覆盖。

## 集群

最典型集群模式： `Master/Slave 模式（主备模式）`。在这种模式中，通常 `Master` 服务器作为主服务器提供写服务，其他的 `Slave` 服务器从服务器通过异步复制的方式获取 `Master` 服务器最新的数据提供读服务。

但是，在 `ZooKeeper` 中没有选择传统的 `Master/Slave`，而是引入了 `Leader Follower Observer` 三种角色。如下图所示

![ZooKeeper 集群角色][5]

`ZooKeeper` 集群中的所有机器通过一个 `Leader` 选举过程来选定一台称为 `Leader` 的机器，`Leader` 既可以为客户端提供写服务又能提供读服务。除了 `Leader` 外，`Follower` 和 `Observer` 都只能提供读服务。`Follower` 和 `Observer` 唯一的区别在于 `Observer` 机器不参与 `Leader` 的选举过程，也不参与写操作的「过半写成功」策略，因此 `Observer` 机器可以**在不影响写性能的情况下提升集群的读性能**。

![ZooKeeper 集群角色详情][6]

当 `Leader` 服务器出现网络中断、崩溃退出与重启等异常情况时，`ZAB` 协议就会进人恢复模式并选举产生新的 `Leader` 服务器。这个过程大致是这样的：
1. `Leader election（选举阶段）`：节点在一开始都处于选举阶段，只要有一个节点得到超半数节点的票数，它就可以当选准 `Leader`；
2. `Discovery（发现阶段）`：在这个阶段，`Followers` 跟准 `Leader` 进行通信，同步 `Followers` 最近接收的事务提议；
3. `Synchronization（同步阶段）`：同步阶段主要是利用 `Leader` 前一阶段获得的最新提议历史，同步集群中所有的副本。同步完成之后 准 `Leader` 才会成为真正的 `Leader`；
4. `Broadcast（广播阶段）`：到了这个阶段，`Zookeeper` 集群才能正式对外提供事务服务，并且 `Leader` 可以进行消息广播。同时如果有新的节点加入，还需要对新节点进行同步。

## 参考

[JavaGuide：ZooKeeper 相关概念总结][4]

[1]: https://wiki.blanc.site/archives/f1bf1c1a.html
[2]: https://wiki.blanc.site/archives/a07e5380.html
[3]: https://img.blanc.site//wiki/img/20190711195553.png
[4]: https://snailclimb.top/JavaGuide/#/system-design/framework/ZooKeeper
[5]: https://img.blanc.site//wiki/img/20190711200430.png
[6]: https://img.blanc.site//wiki/img/20190711200643.png