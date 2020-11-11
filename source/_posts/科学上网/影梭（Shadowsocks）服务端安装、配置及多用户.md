---
title: 影梭（Shadowsocks）服务端安装、配置及多用户
date: 2017/10/23
categories:
  - 科学上网
tags:
  - Shadowsocks
copyright: true
---

## 1. 前记

最近由于某些不可抗力，墙变得格外的高，蓝灯、萤火虫等免费翻墙软件间歇性崩溃，最后我实在受不了了就直接自己搭了一个影梭服务端，写下这篇博文记录搭建过程和自己走过的一些坑。

## 2. 服务器选择

学生党，所以去博客：[VPS侦探][1] 找了一个性价比比较高的 `VULTR`，最低配置 &#36;2.5（折合人民币差不多 ￥20），支持支付宝所以不用担心支付问题，但是比较坑的是充值 &#36;10 起充，不过先存着也没坏处。

节点的话我搜了一下网上推荐洛杉矶，我手快买了一个迈阿密的用起来感觉也不慢。

## 3. SSH 搭建服务端

不懂的同学直接略过概念按照步骤走就行了：

### 1. 下载一个 SSH 登录器，我用的是 Xshell，官网可免费下载

### 2. 用 Xshell 登陆

![Xshell 登陆][2]

![Xshell 登陆][3]

### 3. 检查下 Python 版本，要有 2.6 or 2.7

### 4. 安装 pip

### 5. 安装影梭服务端

```powershell
pip install shadowsocks
```

### 6. 建立并完善影梭配置文件

```powershell
vi /etc/shadowsocks.json
# 然后按 i 进入插入模式，输入以下内容：
```

```json
{  
    "server":"my_server_ip"，
    "local_address": "127.0.0.1",
    "local_port":1080,
    "port_password":
{
    "8391": "password1",
    "8392": "password2",
    "8393": "password3",
    "8394": "password4"
},
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

配置文件为 `json` 格式，每一项后面有一个逗号不要忘记！
如果格式错误，试试手动输入。

```
配置相关说明：
server: the address your server listens（服务器IP）
local_address: the address your local listens（本地代理地址）
local_port: local port（本地代理端口）
port_password: password used for encryption(自己设定的服务器端口和密码)
timeout: in seconds（超时断开，以秒为单位）
method: default: “aes-256-cfb”, see Encryption（加密方式）
fast_open: use TCP_FASTOPEN, true / false（是否使用TCP）

输入完毕之后按 Esc 退出编辑模式，接着按 shift+: 进入命令模式，最后输入 wq 保存并退出。
 
配置完毕。
```

### 7. 启动影梭

```powershell
前端启动（不推荐）:
ssserver -c /etc/shadowsocks.json
后端启动（推荐）：
开始：
ssserver -c /etc/shadowsocks.json -d start
结束：
ssserver -c /etc/shadowsocks.json -d stop
```

### 8. 设置开机启动

```powershell
vi /etc/rc.local
# 然后将里面的最后带有ssserver的删除（双击字母d注释掉），然后加入
/usr/bin/ssserver -c /etc/shadowsocks.json -d start
# 再:wq保存退出。
```

## 4. 客户端下载和使用

各系统下载地址：[shadowsocks Clients][4]

## 5. Windows 详细配置

![Windows 详细配置][5]

[1]: https://www.vpser.net/
[2]: https://www.blanc.site/img/71.png
[3]: https://www.blanc.site/img/72.png
[4]: https://shadowsocks.org/en/download/clients.html
[5]: https://www.blanc.site/img/73.png