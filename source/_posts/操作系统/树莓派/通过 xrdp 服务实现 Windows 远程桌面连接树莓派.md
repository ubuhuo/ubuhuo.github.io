---
title: 通过 xrdp 服务实现 Windows 远程桌面连接树莓派
date: 2017/9/8
categories:
  - 操作系统
  - 树莓派
tags:
  - 远程连接树莓派
copyright: true
abbrlink: 903c69be
---

受同学影响，最近接触到了树莓派，又加上自己技痒想试一下这个小东西究竟能做什么，所以开始了树莓派学习之旅。

正题开始 `xrdp` 实现 `Windows` 远程树莓派的具体步骤

## 1. 安装 tightvncserver

```powershell
sudo apt-get install tightvncserver
```

## 2. 安装 xrdp

```powershell
sudo apt-get install xrdp
```

## 3. 打开服务器上的远程桌面访问端口

```powershell
sudo ufw allow 3389
```

## 4. 重启两个服务

```powershell
sudo service ufw restart
sudo service xrdp restart
```

## 5. Windows 系统远程链接配置

注意用户名正确填写

![Windows 系统远程链接配置][1]

[1]: https://www.blanc.site/img/50.png