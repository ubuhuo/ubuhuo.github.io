---
title: Linux 下的压缩和解压缩
date: 2019/7/20
categories:
  - 1-网络笔记
tags:
  - Linux
copyright: true
---

`Linux` 下常见的压缩文件格式有：

```
.tar .gz .tar.gz .tgz .bz2 .tar.bz2 .Z .tar.Z .zip .rar
```

下面我们就分别讲一下在 `Linux` 下如何用不同的方式对文件进行打包、压缩和解压缩

## tar

`Linux` 下最常用的打包程序就是 `tar` 了，使用 `tar` 程序打出来的包我们常称为 `tar包`，tar包 文件的命令通常都是以 `.tar` 结尾的。生成 `tar包` 后，就可以用其它的程序来进行压缩了。

### 基本用法：

#### 打包 -c

```
# 将所有 .jpg 文件打包为 all.tar
tar -cf all.tar *.jpg
# -c 表示产生新的包
# -f 指定包的文件名
```

#### 添加 -r

```
# 将所有 .gif 文件添加到 all.tar
tar -rf all.tar *.gif
# -r 增加文件
# -f 指定包的文件名
```

#### 更新 -u

```
# 更新 all.tar 中 logo.gif 文件
tar -uf all.tar logo.gif
# -u 更新文件
# -f 指定包的文件名
```

#### 列出 -t

```
# 列出 all.tar 中所有文件
tar -tf all.tar
# -t 列出文件
# -f 指定包的文件名
```

#### 解包 -x

```
# 解包 all.tar 中所有文件
tar -xf all.tar
# -x 解包
# -f 指定包的文件名
```

### tar 与其他压缩程序

为了方便用户在打包解包的同时可以压缩或解压文件，`tar` 提供了一种特殊的功能：可以在打包或解包的同时调用其它的压缩程序，比如：`gzip` `bzip2` 等。

#### tar 与 gzip

`gzip` 是 `GNU` 组织开发的一个压缩程序，`.gz`结尾的文件就是 `gzip` 压缩的结果。

单纯的 `.gz` 用 `gunzip` 命令解压

`tar` 中使用 `-z` 这个参数来调用 `gzip`，举例说明：

##### 打包并使用 gzip 压缩

```
tar -czf all.tar.gz *.jpg
# -c 表示产生新的包
# -z 调用 gzip
# -f 指定包的文件名
```

##### 使用 gzip 解压缩并解包

```
tar -xzf all.tar.gz
# -x 解包
# -z 调用 gzip
# -f 指定包的文件名
```


#### tar 与 bzip2

`bzip2` 是一个压缩能力更强的压缩程序，`.bz2` 结尾的文件就是 `bzip2` 压缩的结果。

单纯的 `.bz2` 用 `bunzip2` 命令解压

`tar` 中使用 `-j` 这个参数来调用 `bzip2`，举例说明：

##### 打包并使用 bzip2 压缩

```
tar -cjf all.tar.bz2 *.jpg
# -c 打包
# -j 调用 bzip2
# -f 指定包的文件名
```

##### 使用 bzip2 解压缩并解包

```
tar -xjf all.tar.bz2
# -x 解包
# -j 调用 bzip2
# -f 指定包的文件名
```

#### tar 与 compress 

`compress` 也是一个压缩程序，但使用人数并不多，`.Z` 结尾的文件就是 `compress` 压缩的结果。

单纯的 `.Z` 用 `uncompress` 命令解压

`tar` 中使用 `-Z` 这个参数来调用 `compress`，举例说明：

##### 打包并使用 compress 压缩

```
tar -cZf all.tar.Z *.jpg
# -c 打包
# -Z 调用 compress
# -f 指定包的文件名
```

##### 使用 compress 解压缩并解包

```
tar -xZf all.tar.Z
# -x 解包
# -Z 调用 compress
# -f 指定包的文件名
```

### 总结 tar 知识

```
-c 打包
-x 解包 
-t 查看内容 
-r 向压缩归档文件末尾追加文件 
-u 更新原压缩包中的文件
以上五个是独立的命令参数，可以和别的命令连用但只能用其中一个

下面的参数是根据需要在压缩或解压时按需搭配
-z 调用 gzip 
-j 调用 bzip2 
-Z 调用 compress 
-v 显示过程 
-O 将文件解压到标准输出 

-f 为必要参数，所有命令都必须标明
-f 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名 
```

## zip 和 rar

`.zip` 和 `.rar` 是 `Window` 下的常见压缩文件，`Linux`也有相应的方法来解压它们：

### zip 

`Linux` 下提供了 `zip` 和 `unzip` 程序，`zip` 是压缩命令程序，`unzip` 是解压命令程序。它们的参数选项很多，这里只做简单介绍：

#### 压缩

```
# 将所有 .jpg 文件压缩成一个 zip 包
zip all.zip *.jpg
```

#### 解压

```
# 将 all.zip 中的所有文件解压出来
unzip all.zip
```

### rar

要在 `Linux` 下处理 `.rar` 文件，需要安装 `RAR for Linux`，可以从网上下载

下载地址：[http://www.rarsoft.com/download.htm][1]

安装后就有了 `rar` 和 `unrar` 这两个命令程序，`rar` 压缩，`unrar` 解压。依旧只做简单介绍：

#### 压缩

```
# 将所有 .jpg 文件压缩成 all.rar，该程序会将 .rar 扩展名将自动附加到包名后
rar a all *.jpg
```

#### 解压

```
# 将 all.rar 中的所有文件解压出来
unrar e all.rar
```

## 总结

到此为至，已经介绍了 `Linux` 下的 `tar gzip gunzip bzip2 bunzip2 compress uncompress zip unzip rar unrar` 等命令工具，使用它们可以对 `.tar 、.gz、.tar.gz、.tgz、.bz2、.tar.bz2、. Z、.tar.Z、.zip、.rar` 这 10种 压缩文件进行解压。


[1]: http://www.rarsoft.com/download.htm