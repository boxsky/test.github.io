---
title: Kibana安装
date: 2021-01-21 22:10:38
tags:
- kibanba
- 安装
categories:
- kibanba
---


### 下载

Kibana官方下载地址：[https://www.elastic.co/cn/downloads/past-releases/#kibana](https://www.elastic.co/cn/downloads/past-releases/#kibana)

根据ElasticSearch版本及安装环境下载相应的Kibana安装包。一般来说kibana版本号和对应es版本号一致即可，这里我es使用的是5.6.16，kibana也选的是5.6.16,选好对应版本后复制链接地址

```bash
wget https://artifacts.elastic.co/downloads/kibana/kibana-5.6.16-linux-x86_64.tar.gz
```

### 安装及配置

解压之后到安装目录下的config下，编辑kibana.yml配置文件，添加如下配置：

```bash
#配置本机ip
server.host: "127.0.0.1"
#配置es集群url
elasticsearch.url: "http://127.0.0.1:9200"
#kibana端口
server.port: 5601
```