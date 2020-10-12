---
title: linux下安装go语言环境
date: 2020-10-12 11:55:21  
tags:
- linux
- go
- 环境
categories:
- go
---

安装Go环境
> go语言中文网：https://studygolang.com/dl

打开官网下载地址选择对应的系统版本, 复制下载链接
这里我选择的是
go1.15.2.linux-amd64：https://dl.google.com/go/go1.15.2.linux-amd64.tar.gz

```bash
wget https://dl.google.com/go/go1.10.3.linux-amd64.tar.gz
```

下载完成

> 执行tar解压到/usr/loacl目录下，得到go文件夹
```bash
tar -C /usr/local -zxvf  go1.15.2.linux-amd64.tar.gz
```

> 添加/usr/loacl/go/bin目录到PATH变量中。添加到/etc/profile 

```bash
vim /etc/profile
```

> 在最后一行添加
```bash
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```

> wq保存退出后source一下
```bash
source /etc/profile
```

执行go version，如果现实版本号，则Go环境安装成

