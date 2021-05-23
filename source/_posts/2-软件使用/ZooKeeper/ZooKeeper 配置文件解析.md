---
title: ZooKeeper 配置文件解析
date: 2019/7/11
categories:
  - 2-软件使用
  - ZooKeeper
tags:
  - ZooKeeper
copyright: true
---

## 最低配置

以下是必须在配置文件中定义的最低配置选项：

1. `tickTime`: `ZooKeeper` 中使用的基本时间单元, 以「毫秒」为单位, 默认值是 `2000`。它用来调节心跳和超时。例如, 默认的会话超时时间是两倍的 `tickTime`。
2. `dataDir`: `ZooKeeper` 用来存储内存数据库快照的目录, 并且除非指定其它目录, 否则数据库更新的事务日志也将会存储在该目录下。建议配置 `dataLogDir` 参数来指定 `ZooKeeper` 事务日志的存储目录。
3. `clientPort`: 服务器监听客户端连接的端口, 也即客户端尝试连接的端口, 默认值是 `2181`。
4. `secureClientPort`：服务器监听通过 `SSL` 连接的客户端端口。`clientPort` 指定明文连接的端口，而 `secureClientPort` 指定 `SSL` 连接的端口。

## 高级配置

1. `dataLogDir`: 事务日志的存储目录，设置日志设备对吞吐量和稳定的延迟有很大的影响。建议将事务日志单独存储在日志设备中并通过 `dataLogDir` 属性设置存储目录。
2. `globalOutstandingLimit`: 客户端提交请求的速度比 `ZooKeeper` 处理请求的速度要快，特别是在有很多客户端的情况下。为了防止 `ZooKeeper` 由于排队的请求而耗尽内存，`ZooKeeper` 将限制客户端请求数为 `globalOutstandingLimit`。默认为`1000`。对应 `Java` 系统属性: `zookeeper.globalOutstandingLimit`。
3. `preAllocSize`: 
4. `snapCount`: 
5. `maxClientCnxns`: 在 `socket` 级别限制单个客户端与单台服务器之前的并发连接数量, 可以通过 `IP地址` 来区分不同的客户端。它用来防止某种类型的 `DoS攻击`, 包括文件描述符耗尽。默认值是 `60`。将其设置为 `0` 将完全移除并发连接数的限制。
6. `clientPortAddress`: 
7. `minSessionTimeout`: 
8. `maxSessionTimeout`: 
9. `fsync.warningthresholdms`: 
10. `autopurge.snapRetainCount`: 配置 `ZooKeeper` 在自动清理的时候需要保留的数据文件快照的数量和对应的事务日志文件, 默认值是 `3`。
11. `autopurge.purgeInterval`: 和参数 `autopurge.snapRetainCount` 配套使用, 用于配置 `ZooKeeper` 自动清理文件的频率, 默认值是 `1`, 即默认开启自动清理功能, 设置为 `0` 则表示禁用自动清理功能。
12. `syncEnabled`: 
13. `zookeeper.extendedTypesEnabled`: 
14. `zookeeper.emulate353TTLNodes`: 
15. `serverCnxnFactory`: 

## 集群选项

1. `electionAlg`: 
2. `initLimit`: 默认值是 `10`, 即 `tickTime` 属性值的 10倍。它用于配置允许 `followers` 连接并同步到 `leader` 的最大时间。如果 `ZooKeeper` 管理的数据量很大的话可以增加这个值。
3. `leaderServes`: 
4. `server.x=[hostname]:nnnnn[:nnnnn]`: 
5. `syncLimit`: 默认值是 `5`, 即 `tickTime` 属性值的 5倍。它用于配置 `leader` 和 `followers` 间进行心跳检测的最大延迟时间。如果在设置的时间内 `followers` 无法与 `leader` 进行通信, 那么 `followers` 将会被丢弃。
6. `group.x=nnnnn[:nnnnn]`: 
7. `weight.x=nnnnn`: 
8. `cnxTimeout`: 
9. `standaloneEnabled`: 
10. `reconfigEnabled`: 
11. `4lw.commands.whitelist`: 
12. `tcpKeepAlive`: 
**
## 参考

[ZooKeeper 官网][1]
[珍爱矢豆：ZooKeeper 安装和配置][2]

[1]: http://zookeeper.apache.org/doc/current/zookeeperAdmin.html
[2]: https://www.jianshu.com/p/de90172ea680