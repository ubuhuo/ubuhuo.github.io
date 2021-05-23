---
title: Cisco IOS 系列-0-概览
date: 2021/04/05
categories:
  - 3-计算机科学
  - 2-计算机系统
  - 2-操作系统
  - CiscoIOS
tags:
  - Cisco IOS
  - 系列文章
copyright: true
---

## 简介

> Cisco Internetwork Operating System(IOS) 是思科公司为其网络设备开发的操作维护系统。用户可以通过命令行界面对网络设备进行功能设定，提供的功能大致为一下几点：
> 1. 网络设备及连接端口的功能参数设置；
> 2. 设置、运行网络协议和网络功能；
> 3. 设备间资料传输；
> 4. 安全管理；
> 
> 来自[维基百科：思科IOS][1]

## 路由器组件及功能

## 设备管理方法

可通过多种方法访问 CLI 环境，最常用的方法有：
1. 通过 Console 接口管理设备
2. 通过 Telnet 或 SSH 远程管理设备
3. 辅助端口

## 操作模式

![操作模式][2]

### 用户执行模式 | 用户模式

正常登陆设备 CLI 后的默认配置模式，只具备最基本的查看权限

```
cisco> ...
```

### 特权执行模式 | 特权模式

通过用户认证后即可进入特权模式，能够查看当前设备的全部配置信息

```
cisco# ...
```

### 全局配置模式

在特权模式下，使用命令 `config terminal` 进入全局配置模式，在此模式下可配置设备全局参数，开启或关闭设备全局特性或功能；也可从此模式进入多种不同的其他子配置模式。

```
cisco(config)# ...
```

### 其他配置模式

#### line 模式 | 线路模式、行模式

```
cisco(config)# line ...
cisco(cofnig-line)# ...

cisco(config)# line ?
可以通过如上命令查看 line 模式下支持的配置选项
```

#### interface 模式 | 接口模式

针对设备的某一类端口（可以针对某一具体端口也可针对某一类端口的多个端口）进行配置，例如：

```
cisco(config)# interface ...
cisco(config-if)# ...

cisco(config)# interface ?
可以通过如上命令查看 interface 模式下支持的配置选项
```

#### router 模式 | 路由进程配置模式

```
cisco(config)# router ...
cisco(config-router)# ...

cisco(config)# router ?
可以通过如上命令查看 router 模式下支持的配置选项
```

## 使用 CLI 帮助

![Cisco IOS 命令结构][3]

### 命令提示及补全

#### 命令提示

Cisco IOS 系统的命令提示符是 `?`
在命令未输入完成之前输入 `?` 系统会提示你所有可能的命令
在命令输入完成后输入 `?` 系统会提示你所有当前命令可接参数形式以及具体功能

```
R1(config)# c?
cdp class-map clock config-register crypto

R1(config)# router ?
bgp     .... 
eigrp   ....
ospf    ....
rip     ....
```

#### 命令补全

Cisco IOS 系统的命令补全按钮是 Tab 键

### 命令语法检查

```
R1(config)# router ospf
% Incomplete command. // 命令不完整

R1(config)# router ospd 1
                      ^
% Invalid input detected at '^' marker.  // 箭头所指字符无法识别

R1(config)#s
% Ambiguous command: "s" // 未知的输入 
```

### 热键和快捷方式

## 参考

1. [维基百科：思科 IOS][1]

[1]: https://zh.wikipedia.org/wiki/%E6%80%9D%E7%A7%91IOS
[2]: https://img.blanc.site//wiki/img/20210415111913.png
[3]: https://img.blanc.site//wiki/img/20210415114649.png