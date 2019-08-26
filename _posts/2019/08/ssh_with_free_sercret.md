---
title: windows下ssh免密登录配置
date: 2019/8/26 12:01:00
tags:
- ssh
categories:
- 运维
- linux
---

#### 之前登录自己的阿里云服务器一直使用的是ssh,输入密码进行登录,也就是使用ssh的基于口令验证的登录方式进行的登录,如:
```
ssh -p 22 root@IP
```
#### 这种方式其实存在很多弊端,不仅每次登录需要输入密码,而且还容易受到类似"中间人攻击"的攻击,其实还可以使用另一种基于秘钥验证的ssh登录方式进行登录,首先你需要在你的电脑创建一对秘钥,然后把你的公钥放到需要访问的服务器上，这样你通过ssh进行秘钥登录时,服务器收到请求之后，先在该服务器上你的主目录下寻找你的公钥，然后把它和你发送过来的公钥进行比较。如果两个密钥一致，服务器就用公钥加密“质询”(challenge)并把它发送给客户端(也就是你的电脑),客户端软件收到“质询”之后就可以用你的私钥在本地解密再把它发送给服务器完成登录。与第一种级别相比，第二种级别不仅加密所有传输的数据，也不需要在网络上传送口令，因此安全性更高，可以有效防止中间人攻击。

####  下面进行实操:
- 首先生成秘钥对
```
ssh-keygen
```
![sshkey](http://img.zhaobo.top/2019/08/ssh_with_free_sercret/1.png "sshkey")
> 连续enter，可以在当前系统用户文件夹的.ssh文件夹下生成公私钥文件（默认为id_rsa、id_rsa.pub，其中id_rsa为私钥文件、id_rsa.pub为公钥文件） 
> 这里我在windows下使用的是 [cmder](https://cmder.net)命令行工具,还比较好用,当你的电脑装了git之后你就可以使用 `ssh-keygen`命令

- 将公钥文件(.ssh文件夹中的id_rsa.pub文件)中的内容,拷贝到服务器端`~/.ssh`下的`authorized_keys`中 若`authorized_keys`文件不存在请先自行创建
![id_rsa.pub](http://img.zhaobo.top/2019/08/ssh_with_free_sercret/2.png "id_rsa.pub")
- 此刻不出意外的话,就可以进行免密登录了
```
ssh root@remoteip
```
![ssh免密登录](http://img.zhaobo.top/2019/08/ssh_with_free_sercret/3.png "ssh免密登录")

- 但是这样每次还得记住服务器ip,如果有多台服务器就很麻烦,这里我们可以设置一个别名,进行登录,把配置文件写入到客户端.ssh文件夹下的config文件中
```
Host box #指定别名
    Hostname ip或者域名
    Port 22 #ssh默认端口
    User username #指定用户名 我这里用的是root
    IdentityFile id_rsa    #指定私钥文件路径
```
- 保存之后,就可以使用别名直接登录
```
ssh box
```
![ssh别名免密登录](http://img.zhaobo.top/2019/08/ssh_with_free_sercret/4.png "ssh别名免密登录")




