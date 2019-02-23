---
title: VMware 安装 CentOS 后网络设置
date: 2018/6/30
categories:
  - 软件使用
  - VMware
tags:
  - VMware 安装 CentOS 后网络设置
copyright: true
abbrlink: 4dedf7b2
---

这是一个适用性比较小解决方法，若此方法不能解决你遇到的问题请自行百度其他方法。

打开虚机看看虚机的网络设置了。命令如下

```
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

其中部分内容如下：

```
DEVICE=eth0  #设备名称，可根据ifcofnig命令查看到。
BOOTPROTO=dhcp  #连接方式，dhcp会自动分配地址，此时不需要在下面设置ip和网关
HWADDR=00:0C:29:AD:66:9F  #硬件地址，可根据ifcofnig命令查看到。
ONBOOT=yes  #yes表示启动就执行该配置，需要改为yes
```

可用 `ifconfig` 命令获取相关信息。

修改完后需要重启网络设置

```
service network restart
```
