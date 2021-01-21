---
title: beego框架使用问题汇总
date: 2020-10-13 14:09:21  
tags:
- beego
- go
- 问题
categories:
- go
---

>  执行`go get github.com/beego/bee`安装过程中报错
```bash
package golang.org/x/sys/unix: unrecognized import path "golang.org/x/sys/unix" (https fetch: Get https://golang.org/x/sys/unix?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
``` 

- 说明被墙

```bash
cd ~/go/src
mkdir -p golang.org/x
cd golang.org/x
git clone https://github.com/golang/sys.git
```

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

