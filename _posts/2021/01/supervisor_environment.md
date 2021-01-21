---
title: supervisor启动项目找不到环境变量的问题
date: 2021-01-21 22:50:19
tags:
- supervisor
- 环境变量
categories:
- supervisor
---

### 找不到已经在/etc/profile里配置好的环境变量

我这里遇到的是，使用supervisor启动es的时候，没有找到JAVA_HOME，而直接使用命令启动es是可以的，说明是supervisor的配置问题，解决办法如下：
打开对应的supervisor启动的项目配置文件(一般是在supervisor目录下的profile文件夹内),
```
[program:xxx]
environment=JAVA_HOME="/xxxxx/jdkxxxx"
```