---
title: 新部署一台Linux云服务器时常用的命令
date: 2018/5/12
categories:
  - 操作系统
  - Linux
tags:
  - Linux 常用的命令
copyright: true
abbrlink: e94a0a36
---

每次部署新的云服务器总是会进行一些流程化的东西，现在还不会写脚本所以只能一个一个的码命令，但是有些命令很少会用到所以每次都要去查一遍，干脆就搬运到自己的博客里以供下次使用。

根据一下命令可以进行的操作：拿到新服务器 -> SSH 远程连接 root -> 新建一个用户组 -> 新建一个用户并添加到新建的用户组 -> 设置新用户密码 -> 添加新用户到 sudoer -> 开启一个随机端口号 -> 重启防火墙- > 修改 SSH 登陆端口号为随机端口号 -> 重启 SSH

## firewall

| 命令      |     作用 |
| :--------: | :--------:|
| `firewall-cmd --state` |   查看防火墙状态，是否是 running |
| `firewall-cmd --reload` |   重新载入配置，比如添加规则之后，需要执行此命令 |
| `firewall-cmd --get-zones` |   列出支持的 zone |
| `firewall-cmd --get-services` |   列出支持的服务，在列表中的服务是放行的 |
| `firewall-cmd --query-service ftp` |   查看 FTP 服务是否支持，返回 yes 或者 no |
| `firewall-cmd --add-service=ftp` |   临时开放 FTP 服务 |
| `firewall-cmd --add-service=ftp --permanent` |   永久开放 FTP 服务 |
| `firewall-cmd --remove-service=ftp --permanent` |   永久移除 FTP 服务 |
| `firewall-cmd --add-port=80/tcp --permanent` |   永久添加 80 端口 |
| `iptables -L -n` |   查看规则，这个命令是和 iptables 的相同的 |
| `man firewall-cmd` |   查看帮助 |

## linux创建用户（组）并设置密码

添加用户：`useradd -m username`  
设置密码 ：`passwd username`
删除用户：`userdel  -r  username`
添加用户组：`groupadd testgroup`
删除用户组：`groupdel testgroup`

## Linux给用户添加 sudo 权限
```powershell
/etc/sudoers
找到："root ALL=(ALL) ALL"
在下面添加"xxx ALL=(ALL) ALL"(xxx是你的用户名)，保存。
```

## 修改默认 SSH 端口
```powershell
vi /etc/ssh/sshd_config
systemctl restart sshd.service
```

## 设置 SSH 不自动断开连接

```powershell
vim /etc/ssh/sshd_config
# 修改两处的值为：
ClientAliveInterval 60
ClientAliveCountMax 10
# 使修改的ssh配置文件生效：
service sshd reload
```