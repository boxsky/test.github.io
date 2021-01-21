---
title: 同步mysql从库数据到elasticsearch
date: 2021-01-21 22:35:19
tags:
- elasticsearch
- mysql
- 同步
categories:
- elasticsearch
---

A>B>C

A为主库

B为从库

C为ES

编辑从服务器的配置文件：/etc/my.cnf

B服务器除了需要打开log-bin之外，还需要打开[log-slave-updates](http://dev.mysql.com/doc/refman/5.0/en/replication-options-slave.html#option_mysqld_log-slave-updates)选项，你可以再B上使用“show variables like 'log%';”来确认是否已经生效。

binlog_fomat=row #为了使用mysql→es同步工具

编辑从服务器的配置文件：/etc/my.cnf

### 安装go

```bash
cd /www/server
wget https://golang.google.cn/dl/go1.15.2.linux-amd64.tar.gz
tar -zxvf go1.15.2.linux-amd64.tar.gz
```

**配置环境变量 vi /etc/profile**

```
vim /etc/profile
#文件追加

export GOROOT=/www/server/go
export GOPATH=/www/server/gohome 
export PATH=$PATH:/www/server/go/bin

#执行source 
 source /etc/profile
```

### go-mysql-elasticsearch安装

```bash
#安装依赖包
yum -y install gettext-devel openssl-devel perl-CPAN perl-devel zlib-devel
#安装git
yum -y install git

go env -w GOPROXY=https://goproxy.cn
go get github.com/siddontang/go-mysql-elasticsearch

**#进入目录执行安装
cd $GOPATH/src/github.com/siddontang/go-mysql-elasticsearch

make
#如果提示入下
GO111MODULE=on go build -o bin/go-mysql-elasticsearch ./cmd/go-mysql-elasticsearch
则执行入下命令

#配置river.toml

#mysql 配置
my_addr = "127.0.0.1:3306"
my_user = "root"
my_pass = "112233@"

# Elasticsearch
es_addr = "127.0.0.1:9212"

# 存儲数据的位置
data_dir = "/var"

# Inner Http status address
stat_addr = "127.0.0.1:12800"
stat_path = "/es"

# 数据源配置
# 以slave模式工作
server_id =  10001
# mysql/mariadb
flavor = "mysql"

#mysqldump 路径, 如果为空或者未设置,会跳过这一环节
mysqldump = "mysqldump"
bulk_size = 128
flush_bulk_time = "200ms"
skip_no_pk_table = false

#mysql 数据源
[[source]]
#数据库名称
schema = "test_es"
#数据表同步范围,支持通配符
tables = ["orders"]

#规则定义
[[rule]]
#数据库名称
schema = "test_es"

#规则对应的数据表,支持通配符
table = "orders"
# 目标 ES 索引
index = "orders"

type = "orders_db"**
```

> 部分问题解决参考:
[https://github.com/siddontang/go-mysql-elasticsearch/issues/375](https://github.com/siddontang/go-mysql-elasticsearch/issues/375)