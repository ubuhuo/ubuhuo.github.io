---
title: ZooKeeper 命令
date: 2019/7/11
categories:
  - 软件使用
  - ZooKeeper
tags:
  - ZooKeeper 命令
copyright: true
abbrlink: 2b7b1ffc
---

## zkServer

1. 启动 zkServer：`zkServer.sh start`
2. 查看 zkServerr：`zkServer.sh status`
3. 停止 zkServer：`zkServer.sh stop`
4. 重启 zkServer：`zkServer.sh restart`

## zkCli

1. 启动 zkCli：`zkCli.sh -server localhost:2181`

## Client 命令行模式下


| 命令 | 功能 |
| --- | --- |
| h | 显示所有命令 |
| ls path | 查看某个节点下的所有子节点信息 |
| stat path | 获取指定节点的状态信息|
| get path | 获取当前节点的数据内容 |
| ls2 path | ls 和 stat 两个命令的结合 |
| create [-s] [-e] path data acl | 创建节点 |
| quit | 退出客户端 |
| set path data [version] | 修改当前节点的数据内容  如果指定版本，需要和当前节点的数据版本一致 |
| delete path [version] | 删除指定路径的节点，如果有子节点要先删除子节点 |
| rmr path  | 删除当前路径节点及其所有子节点 |
|  setquota -n｜-b val path | 设置节点配额（比如限制节点数据长度，限制节点中子节点个数）|
| listquota path | 查看路径节点的配额信息 |
| delquota [-n｜-b] path  | 删除节点路径的配额信息 |
| connect host:port 和 clost | 在当前连接中连接其他的 ZooKeeper 服务器和关闭服务器 |
| history 和 redo cmdno | 查看客户端这次会话所执行的所有命令 和 执行指定历史命令 |

### 节点状态信息

| 状态代码 | 状态信息  |
| --- | --- |
| czxid | 创建该节点的事务 id |
| ctime | 创建该节点的时间 |
| pZxid | 操作当前节点的子节点列表的事务 id |
| cversion | 当前节点的子节点版本号 |
| dataVersion | 当前节点的数据版本号 |
| aclVersion | 当前节点的 acl 权限版本号 |
| ephemeralowner | 当前节点的如果是临时节点，该属性是临时节点的事务 id |
| dataLength | 当前节点的数据长度 |
| numchildren | 当前节点的子节点个数 |


## 四字命令

`ZooKeeper` 支持某些特定的四字命令字母与其的交互。它们大多是查询命令，用来获取 `ZooKeeper` 服务的当前状态及相关信息。


| 四字命令 | 命令功能 |
| --- | --- |
| conf | 输出相关服务配置的详细信息 |
| cons | 列出所有连接到服务器的客户端的全部连接/会话信息 |
| dump | 列出未经处理的会话和临时节点 |
| envi | 输出关于服务环境的详细信息（区别于 conf 命令） |
| reqs | 输出相关服务配置的详细信息 |
| ruok | 测试是否启动了该 Server，若回复 imok 表示已经启动 |
| stat | 来查看哪个节点被选择作为 follower 或者 leader |
| wchs | 列出服务器 watch 的详细信息 |
| wchc | 通过 session 列出服务器 watch 的详细信息 |
| wchp | 通过路径列出服务器 watch 的详细信息 |

使用方式：`echo stat | nc 127.0.0.1 2181`

## 参考

[三丰SanFeng：zookeeper命令行(zkCli.sh&zkServer.sh)使用及四字命令][1]
[RedAnts：ZooKeeper 之 zkCli.sh客户端的命令使用][2]

[1]: https://www.cnblogs.com/linuxbug/p/4840135.html
[2]: https://www.cnblogs.com/chengxuyuanzhilu/p/6698059.html