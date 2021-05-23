---
title: Linux 下安装 Node.js
date: 2017/12/11
categories:
  - 1-网络笔记
tags:
  - Linux
  - Node.js
copyright: true
---

## 1. 选择合适的 Node.js 安装包

从 [Node.js官网][1] 给出的列表中挑选适合的版本和安装包并复制下载链接

![安装包选择][2]

以 `64` 位 `v9.2.1` 版本为例：

[下载地址][3]

## 2. 安装

以 `64` 位 `v9.2.1` 版本的为例

进入到命令行：

```powershell
# 进入到用户目录下
cd ~   
 
# 下载选好的node压缩包
wget https://nodejs.org/dist/v9.2.1/node-v9.2.1-linux-x64.tar.xz
 
# 等待下载完成后解压安装包，因为我下载的安装包是两层压缩所以要解压两次
xz -d node-v9.2.1-linux-x64.tar.xz
 
# 解压后得到node-v9.2.1-linux-x64.tar
tar -xf node-v9.2.1-linux-x64.tar
 
# 解压后得到
node-v9.2.1-linux-x64
 
# 将node文件转移到/usr/local/node目录下
mv node-v9.2.1-linux-x64 /usr/local/node
 
# 配置环境变量
vi /etc/profile
 
# 在export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL前插入如下代码：
export NODE_HOME=/usr/local/node
export PATH=$NODE_HOME/bin:$PATH
# 注意等号两遍不要有空格
 
# 完成后退出重启profile
source /etc/profile
```

## 3. 安装成功测试

```powershell
# 至此 nodejs 就安装完成了，可以输入命令查看 node 版本
node -v
# 若安装成功会显示 V9.2.1
```

[1]: https://nodejs.org/dist/
[2]: https://www.blanc.site/img/87.png
[3]: https://nodejs.org/dist/v9.2.1/node-v9.2.1-linux-x64.tar.xz