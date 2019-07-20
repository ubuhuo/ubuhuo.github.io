---
title: CentOS 7 安装 Java 环境
date: 2019/7/20
categories:
  - 操作系统
  - Centos
tags:
  - CentOS 7 安装 JAVA 环境
copyright: true
---

本文主要讲述在 `CentOS 7` 下如何安装并部署 `Java` 环境

> 从2015年4月16日开始，Oracle JDK许可证已更改。
> 
> 新许可证允许某些用途（例如个人使用和开发用途）免费使用 - 但根据以前的 Oracle JDK 许可证授权的其他用途可能不再可用。
> 
> 截取自 Oracle Java 下载网址（机翻）

## 下载

下载地址：[点击跳转][1]

![接受许可协议][2]

![根据系统情况选择要下载的文件][3]

这里要根据你的系统情况选择不同文件，另外还要注意看你选择的文件的后缀，如果不懂的话尽可能选后缀为 `.tar.gz` 的

点击之后他会提醒你进行登录，如果你没有账号则需要先注册一个账号再进行登录

貌似是不登录没办法下载（我个人懒得研究了）

先在本地下载然后通过 `FTP` 工具传输到 `CentOS` 系统中

也可以复制带有授权码的下载链接使用命令行工具 `wget` 下载

## 安装

### 1. 将文件解压到当前目录

```
tar -zxvf jdk-8u221-linux-x64.tar.gz
```

注意这里具体的文件名叫什么与你当时下载的版本有关

### 2. 将文件转移到常用软件目录

```powershell
mv ./jdk1.8.0_221/ /usr/local/java/
```

## 设置环境变量

### 1. 使用 `vim` 打开文件系统环境文件

```
vim /etc/profile
```

### 2. 在末尾添加

```
export JAVA_HOME=/usr/local/java
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

### 3. 使环境变量生效

```
source /etc/profile
```

### 4. 添加软连接

```
ln -s /usr/local/java/bin/java /usr/bin/java
```

### 4. 检查是否生效

```
java -version
```

## 参考

[游魂：CentOS 7 安装 JAVA环境（JDK 1.8）][4]

[1]: https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[2]: https://img.blanc.site//wiki/img/20190721000508.png
[3]: https://img.blanc.site//wiki/img/20190721000802.png
[4]: https://www.iyouhun.com/post-124.html