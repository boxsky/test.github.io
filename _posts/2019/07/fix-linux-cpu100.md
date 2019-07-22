---
title: 记录一次Linux服务器Cpu占用100%多的处理过程
date: 2019/7/22 10:46:25
tags:
- 运维
categories:
- 运维
- linux
---

### 某天早上像往常一样打开服务器后台的web管理界面(我使用的是宝塔linux),登录上去瞄了一眼突然发现不对,cpu占用率的圆环图变成了红色占用率100%,服务器也没有跑job什么的,cpu怎么会突然保持在100%呢我立马登录了服务器,使用top命令查看
```
$  top
```
![服务器进程](http://img.zhaobo.top/2019/07/fix-linux-cpu100/1.png "服务器进程")

#### 这个`HT8sUy71`的进程占用快要达到200%,因为我是双核服务器（2cpu）,我的第一反应是kill掉这个进程,当我kill掉这个进程之后cpu里面降下来
```
$  kill -9 进程id
```
然而，1分钟之后他又死灰复燃了！我初步判断他可能有守护进程,当这个进程挂掉之后,他的守护进程又把它启动起来,此时我查看了一下定时任务列表
```$xslt
$ crontab -l
```
#### 发现了一个奇怪的定时任务,不过很明显这是一个向远程服务器请求下载sh脚本的定时任务,查看内容如下
![定时任务](http://img.zhaobo.top/2019/07/fix-linux-cpu100/2.png "定时任务")
![定时任务](http://img.zhaobo.top/2019/07/fix-linux-cpu100/3.png "定时任务")
![定时任务](http://img.zhaobo.top/2019/07/fix-linux-cpu100/4.png "定时任务")

#### 现在定位到问题,就是这个定时任务搞得鬼，导致这个进程杀掉再次重启。使用crontab命令将这个定时任务删掉

```$xslt
$ crontab -e
```
#### 进入vim模式,删掉对应的这些命令。然后再次kill掉进程,同时匹配一下tmp目录下的`HT8sUy71`文件并且删掉,然后观察了一会cpu降下来之后就再没起来了。事后我想了一下问题发生的原因,想到了之前遇到过一次挖矿木马cpu也是100%,当时在另一台服务器装redis后,对外开放了6379的端口导致被扫到,由于redis的漏洞很快被侵入挂马,当时参考的是 [http://blog.sina.com.cn/pastlifezhangchilde](http://blog.sina.com.cn/pastlifezhangchilde),这次想了一下,前一段时间redis端口也是不小心对外放行了,才导致了这个问题,赶紧把6379对外的端口关掉了



