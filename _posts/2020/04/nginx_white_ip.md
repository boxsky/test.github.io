---
title: 测试环境nginx设置ip白名单
date: 2020-04-20 17:52:08  
tags:
- 白名单
- nginx
- ip
categories:
- nginx
---

## 背景

在我们开发项目的过程中一般来说会有测试环境和生产环境,部署体系再完备一点的可能还有beta环境和stage环境。一般来说，测试环境中debug是开启的，测试的时候有可能暴露出很多比较重要的信息,一旦被搞破坏的人知道了测试链接地址，那么测试环境相对不太安全。所以在测试环境我们要开启ip白名单,只有通过公司的内网，或内部服务器进行访问才可以。这样的话，一下子降低了很大的外部风险。
## 疑问
 这时可能有人会问，那现在需要在家办公修复一个bug或者比较紧急的项目开发怎么办，难道要给每个开发家里的ip加白名单吗，这种情况最简单的方式是在公司内网做个vpn服务,通过vpn连接公司内网来进行开发工作。
 
## 具体操作
> 一般来说,nginx的配置文件,nginx.conf通过include引入某一文件夹下的所有以.conf结尾的配置文件 `include /www/server/panel/vhost/nginx/*.conf;`
- 打开当前项目的ng配置 xx.conf 
- 配置一下内容
```bash
location / {
                allow xxx.xxx.xxx.xx;
                allow xxx.xxx.xxx.xx;
                deny  all;
}
```
> 如果有伪静态存在 放在一起即可

- 配置完成如下

![nginx配置白名单](http://img.zhaobo.top/2020/04/nginx_white_ip/white_ip.png "nginx配置白名单")

### 相关配置命令

- 白名单设置，访问根目录 
```bash
location / {
                allow 123.34.22.155;
                allow 33.56.32.1/100;
                deny  all;
}
```


- 黑名单设置，访问根目录 
```bash
location / {
                deny 123.34.22.155;
}
```

- 特定目录访问限制
```bash
location /tree/list {
                allow 123.34.22.155;
                deny  all;
}
```






