---
title: elasticsearch安装
date: 2021-01-21 22:10:38
tags:
- elasticsearch
- 安装
categories:
- elasticsearch
---

## 下载安装包

> 下载es对应版本

[https://www.elastic.co/cn/downloads/past-releases/elasticsearch-5-6-16](https://www.elastic.co/cn/downloads/past-releases/elasticsearch-5-6-16)

[https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.16.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.16.tar.gz)

若下载速度太慢，可以本机先下载,然后传到服务器

下载es版本对应所需要的jdk

[https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)

### 创建用户及用户组

```bash
groupadd elasticsearch #创建组

useradd elasticsearch -g elasticsearch #创建组内用户

echo 'es@2021' | passwd --stdin elasticsearch #设置密码
```

### 更改elasticsearch目录归属

```bash
chown -R elasticsearch:elasticsearch /www/server/elasticsearch-5.6.16/ #修改es所在目录归属
```

### 启动es

```bash
cd /www/server/elasticsearch-5.6.16/bin
su elasticsearch
./elasticsearch #前台启动
```

当看到下面的信息，代表已经启动成功了

```bash
[2021-01-15T10:15:08,736][INFO ][o.e.n.Node               ] [] initializing ...
[2021-01-15T10:15:08,843][INFO ][o.e.e.NodeEnvironment    ] [6AHM08F] using [1] data paths, mounts [[/ (/dev/vda1)]], net usable_space [33.1gb], net total_space [39.9gb], spins? [possibly], types [xfs]
[2021-01-15T10:15:08,844][INFO ][o.e.e.NodeEnvironment    ] [6AHM08F] heap size [1.9gb], compressed ordinary object pointers [true]
[2021-01-15T10:15:08,846][INFO ][o.e.n.Node               ] node name [6AHM08F] derived from node ID [6AHM08FVRU-qkI6ylnKFVg]; set [node.name] to override
[2021-01-15T10:15:08,847][INFO ][o.e.n.Node               ] version[5.6.16], pid[70030], build[3a740d1/2019-03-13T15:33:36.565Z], OS[Linux/4.18.0-240.1.1.el8_3.x86_64/amd64], JVM[Oracle Corporation/Java HotSpot(TM) 64-Bit Server VM/1.8.0_271/25.271-b09]
[2021-01-15T10:15:08,847][INFO ][o.e.n.Node               ] JVM arguments [-Xms2g, -Xmx2g, -XX:+UseConcMarkSweepGC, -XX:CMSInitiatingOccupancyFraction=75, -XX:+UseCMSInitiatingOccupancyOnly, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -Djdk.io.permissionsUseCanonicalPath=true, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Dlog4j.skipJansi=true, -XX:+HeapDumpOnOutOfMemoryError, -Des.path.home=/www/server/elasticsearch-5.6.16]
[2021-01-15T10:15:10,241][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [aggs-matrix-stats]
[2021-01-15T10:15:10,241][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [ingest-common]
[2021-01-15T10:15:10,241][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [lang-expression]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [lang-groovy]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [lang-mustache]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [lang-painless]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [parent-join]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [percolator]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [reindex]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [transport-netty3]
[2021-01-15T10:15:10,242][INFO ][o.e.p.PluginsService     ] [6AHM08F] loaded module [transport-netty4]
[2021-01-15T10:15:10,243][INFO ][o.e.p.PluginsService     ] [6AHM08F] no plugins loaded
[2021-01-15T10:15:12,762][INFO ][o.e.d.DiscoveryModule    ] [6AHM08F] using discovery type [zen]
```

然后可以通过curl命令测试，如果返回如下信息 代表安装成功

```bash
[root@iZm5e7nt1pesg4anvl50gqZ ~]# curl http://localhost:9200
{
  "name" : "6AHM08F",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "xuOscDPPRdi9DnM7aMH_oA",
  "version" : {
    "number" : "5.6.16",
    "build_hash" : "3a740d1",
    "build_date" : "2019-03-13T15:33:36.565Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```