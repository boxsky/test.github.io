---
title: mysql指定用户指定对应数据库及权限
date: 2020-03-11 15:26:20   
tags:
- mysql
categories:
- 运维
- mysql
---

#### 在实际的开发过程中会遇到线上的数据库权限控制,比如,开发的小伙伴需要查看线上的数据,此时又不能允许他来直接操作数据库进行修改(当然,主要是防止误操作等类似行为),线上的数据库理论上配置应该只允许本地访问,这个时候就要配置一个数据库的只读账号,并且设置数据库白名单ip，可以设置为公司的出口ip
```mysql
CREATE USER 'db_prod_read'@'210.22.123.123' IDENTIFIED BY 'pwd';
```
> 这个是创建一个名为 `db_prod_read` 的用户 `210.22.123.123` 允许连接的ip地址（如果 想让所有IP都可以访问，可以将ip换成 ‘*’星号 只允许本地访问的话 是localhost）  `pwd` 为密码

```mysql
GRANT SELECT ON `db_prod`.* TO 'db_prod_read'@'210.22.123.123';
```
> 设置该用户允许访问的数据库为 `db_prod` 设置允许的操作为 `SELECT`

```mysql
flush privileges;
```
> 刷新授权,done




