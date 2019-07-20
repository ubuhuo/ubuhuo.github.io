---
title: CentOS7 + Python3 + Django(rest_framework) + MariaDB + nginx + uwsgi 部署 API 开发环境
date: 2018/8/17
categories:
  - 编程框架
  - Django
tags:
  - Django 部署
copyright: true
---

这是一个 `Django` 环境部署教程，也是一个我坑爹的 `Django` 环境部署记录：

开始撸代码之前有两点需要提醒：

1. 本教程完美适配如题开发环境但不局限于此环境，其他开发环境仅需将命令操作转换为当前环境下的命令即可；
2. 我写作的顺序是我个人体验比较好的操作顺序仅供参考；

开撸：

## 1. MariaDB 的安装与部署

```powershell
# yum 安装
yum -y install mariadb mariadb-server mariadb-devel

# 安装完成MariaDB，首先启动MariaDB
systemctl start mariadb
# 设置开机启动
systemctl enable mariadb

# 接下来进行MariaDB的相关简单配置
mysql_secure_installation

#首先是设置密码，会提示先输入密码
Enter current password for root (enter for none):# 初次运行直接回车

#设置密码
Set root password? [Y/n] # 是否设置root用户密码，输入y并回车或直接回车
New password: # 设置root用户的密码
Re-enter new password: # 再输入一次你设置的密码

#其他配置
Remove anonymous users? [Y/n] # 是否删除匿名用户
Disallow root login remotely? [Y/n] #是否禁止root远程登录
Remove test database and access to it? [Y/n] # 是否删除test数据库
Reload privilege tables now? [Y/n] # 是否重新加载权限表

#初始化MariaDB完成，接下来测试登录
mysql -uroot -ppassword
```

注意：切记安装 `mariadb-devel` （`Ubuntu` 下是 `mysql-dev` 或 `libmysqlclient-dev`）否则在下面安装 `Python` 的 `mysqlclient` 包的时候可能会出现 `mysql_config not found` 的错误。

## 2. Python3 的安装与部署

在 `CentOS` 中默认安装了 `Python2` 如果你是基于 `Python2` 开发的话可以直接使用，但也要注意版本尽量升级到 `Python2` 的最新版。

### 2.1 安装 Python3 所需依赖

`CentOS` 里面是 `XXX-devel`，如果在 `Ubuntu` 下安装则要改成 `XXX-dev`。

```powershell
# 为 CentOS 系统增加编译功能
yum install -y gcc-c++

# 安装这些模块都是为了成功编译安装 Python3，防止出现各种异常
yum install -y wget openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel xz-devel tk-devel gdbm-devel

# 3.7版本需要一个新的包libffi-devel，安装此包之后再次进行编译安装即可。
yum install -y libffi-devel

# 安装这个模块是为了让 uwsgi 支持使用 -x 选项，能通过 xml 文件启动项目
yum install -y libxml*
```

各个依赖的用途我就不展开讲了，如果好奇的话可以去百度，度娘都知道。

提醒一点：如果你之前已经尝试过一次安装并且失败了，可以看一下是不是少装了某项依赖，在依赖完整的情况下，安装完 `Python3` 后会自动附带安装 `pip`。

### 2.2 编译安装 Python3.X

[点击查看 Python 官方 FTP 地址][1]

```powershell
# 点击上面的官方 FTP 地址选择你想要的版本和压缩方式，获取下载连接
# 本例以 Python3.6.3 为例
# 在合适的路径（推荐在用户根路径下如 /home 或者 /root ）下执行下载命令：
wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz

# 下载完成后执行解压命令：
tar -zxvf Python-3.6.3.tar.gz

# 进入解压后的 Python-3.6.3 文件，依次执行以下命令：
# 编译设置将 Python3 安装到 /usr/local/python3/ 路径下
./configure --prefix=/usr/local/python3
make
make install

# 创建 Python3 软连接，方便直接在命令行使用 Python3 和 pip3 直接调用 Python3 和 pip3
ln -s /usr/local/python3/bin/python3.6 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

### 3.3 利用 pip3 安装必要的 Python 包

```powershell
# 升级 pip3 到最新版本
pip3 install --upgrade pip
# 安装需要的包（ django djangorestframework mysqlclient ) 
pip3 install django djangorestframework mysqlclient
# 创建 django-admin 软连接
ln -s /usr/local/python3/bin/django-admin /usr/bin/django-admin
```

## 3. uwsgi 的安装

本文推荐使用 `pip3` 直接下载安装 `uwsgi` 若想使用其他安装方法请自行百度。

```powershell
# 下载安装 uwsgi
pip3 install uwsgi
# 创建 uwsgi 软连接
ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi3
```

## 4. Django 项目创建或下载

在适合的位置用 `django-admin startproject myproject` 创建自己的项目，或者用 `Git , FTP上传工具` 等上传、克隆自己的项目，本文采用新建项目的方式呈现此过程：

```powershell
# 在合适的地方创建用来存放 Django 项目的目录
mkdir /var/www
# 进入目录
cd /var/www
# 创建 Django 项目
django-admin startproject myproject
# 进入项目
cd myproject
# 查看项目目录
ls
myproject manage.py
# 其中 myproject 为项目配置文件目录 manage.py 为 Django 命令行工具
# 记住我刚刚定义的几个名词：
# 项目目录：/var/www/myproject 
# 项目配置文件目录：/var/www/myproject/myproject 
# 这在后面的配置中有很大的作用千万别搞混
# 下面，进入项目配置文件目录修改部署需要的配置
cd myproject
vim settings.py
# 必须修改的有（本设置不适合在上线项目中使用）
# 1. 修改 ALLPWED_HOSTS = ['*']
# 2. 修改 DATABASES 如下（相应位置改为你的项目的相应值）：
DATABASES = {                                 
    'default': {                              
        'ENGINE': 'django.db.backends.mysql', # 数据库类型
        'NAME': 'test', # 项目数据库名称                    
        'HOST': '127.0.0.1',  # 数据库地址                 
        'PORT': '3306', # 端口号                       
        'USER': 'root', # 数据库用户名                      
        'PASSWORD': 'root', # 数据库密码          
    }                                         
}
# 注意：如果你是上传的自己在本地环境写好的程序别忘了同步数据库
```

## 5. uwsgi 设置

在适当的目录下创建 `uwsgi` 配置文件。
本文采用 `xml` 配置文件，需要更多类型配置文件请自行百度。

```powershell
# 进入项目目录
cd /var/www/myproject
# 创建编辑 uwsgi 配置文件（文件名自起）
vim uwsgiset.xml
# 内容如下
<uwsgi>
    <socket>127.0.0.1:8997</socket><!-- 内部端口，自定义 -->
    <chdir>/var/www/myproject</chdir><!-- 项目目录 -->
    <module>myproject.wsgi</module><!-- wsgi 启动文件 -->
    <processes>4</processes> <!-- 进程数 -->
    <daemonize>uwsgi.log</daemonize><!-- 日志文件路径 -->
    <pidfile>uwsgi.pid</pidfile><!-- uwsgi 管理文件路径，用来重启 uwsgi -->
</uwsgi>
# 更多配置信息请自行百度
# 在项目目录（ /var/www/myproject ）下启动：
uwsgi3 -x uwsgiset.xml
# 提醒 [uWSGI] parsing config file myproject.xml 则启用成功
# 注意仅仅是启用成功
```

## 6. nginx 下载、设置和启用

```powershell
# [必须]安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：
yum install -y gcc-c++

# [必须]PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：
yum install -y pcre pcre-devel

# [自选]zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。
yum install -y zlib zlib-devel

# [自选]nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。
yum install -y openssl openssl-devel

# 官网下载 Ngxin
# 直接下载.tar.gz安装包，地址：https://nginx.org/en/download.html
# 推荐选择稳定版下载 ： 当前版本 1.12.2
# 下载链接： https://nginx.org/download/nginx-1.12.2.tar.gz
wget -c https://nginx.org/download/nginx-1.12.2.tar.gz

# 解压 用到了之前下载的zlib库 若没下载可选择其他方式解压
tar -zxvf nginx-1.12.2.tar.gz

# 编译前初始化配置
cd nginx-1.12.2 # 进入nginx目录
./configure # 使用默认配置

# 编译安装
make
make install

# 查找安装路径
[root@Ryoma nginx-1.12.2]#whereis nginx
nginx: /usr/local/nginx

# 启动、停止 nginx
cd /usr/local/nginx/sbin/ # 进入 nginx sbin 目录
./nginx # 启动 nginx
./nginx -s quit # 此方式停止步骤是待 nginx 进程处理任务完毕进行停止
./nginx -s stop # 此方式相当于先查出 nginx 进程id再使用kill命令强制杀掉进程
./nginx -s reload # 重新加载

#查询 nginx 进程：
ps aux|grep nginx

# 配置 nginx
vim /usr/local/nginx/conf/nginx.conf
# 个人配置图如下
```

![nginx 个人配置图][2]

```powershell
# :wq保存后进入 /usr/local/nginx/sbin/ 目录
# 先检查配置文件是否有错
./nginx -t
# 没有错就执行以下命令：
./nginx
# 终端没有任何提示就证明 nginx 启动成功，可以通过链接查看 nginx 是否启动成功:
# http://192.168.1.111 （请将该ip替换成你的服务器ip）
```

若出现 `nginx` 错误，请自行查阅 `nginx` 错误日志 （默认在 `/usr/local/ngxin/logs/error.log`）
若出现 `internal server error`，请查看 `uwsgi` 日志 （位置请看 `uwsgi xml` 配置文件）

理论上来说到这里你就能看到你的 `Django` 项目启动成功了，如果过程中出现了任何你自己无法解决的问题，欢迎留言或邮件（ryomahan1996#gmail.com）联系我。

[1]: https://www.python.org/ftp/python/
[2]: https://img.blanc.site/wiki/img/1.png