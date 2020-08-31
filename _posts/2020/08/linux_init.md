---
title: linux下walle开机启动服务配置
date: 2020-08-31 11:20:21  
tags:
- linux
- walle
- 发布系统
categories:
- linux
---

> 默认当前已经安装好walle(一个发布系统,http://www.walle-web.io/)

> 我使用的是centos,进入`/etc/init.d/`目录下,创建开机脚本walle

```bash
vi /etc/init.d/walle
```
> 内容如下
```bash
#!/bin/bash
# chkconfig: 2345 55 25
# description: walle Service
cd /www/publishing_system/
sh admin.sh start
```
> 添加权限
```bash
chmod 755 walle
```
```bash
其中2345是默认启动级别，级别有0-6共7个级别。
　　等级0表示：表示关机 　　
　　等级1表示：单用户模式 　　
　　等级2表示：无网络连接的多用户命令行模式 　　
　　等级3表示：有网络连接的多用户命令行模式 　　
　　等级4表示：不可用 　　
　　等级5表示：带图形界面的多用户模式 　　
　　等级6表示：重新启动
55是启动优先级，25是停止优先级，优先级范围是0－100，数字越大，优先级越低。
chkconfig命令可以参考：https://www.runoob.com/linux/linux-comm-chkconfig.html
```
> 添加脚本到开机自动启动项目中
```bash
chkconfig --add walle
chkconfig walle on
```
> 可以通过命令查看是否已添加成功

```bash
chkconfig --list
```
