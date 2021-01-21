---
title: Mysql主从配置
date: 2021-01-21 21:30:26
tags:
- 主从
- 配置
- mysql
categories:
- mysql
---

### **MySQL主从复制原理：**

我们在MySQL中配置了主从之后，**只要我们对Master节点进行了写操作**，这个操作将会被保存到MySQL的binary-log（bin-log）日志当中，当slave连接到master的时候，master机器会为slave开启binlog dump线程。当master 的 binlog发生变化的时候，Master的dump线程会通知slave，并将相应的binlog内容发送给Slave。而Slave节点在主从同步开启的时候，会创建两个线程，一个I/O线程，一个SQL线程。

> I/O线程：该线程链接到master机器，master机器的binlog发送到slave的时候，IO线程会将该日志内容写在本地的中继日志（Relay log）中。SQL线程：该线程读取中继日志中的内容，并且根据中继日志中的内容对Slave数据库做相应的操作。可能造成的问题：在写请求相当多的情况下，可能会造成Slave数据和Master数据不一致的情况，这是因为日志传输过程中的短暂延迟、或者写命令较多，系统速度不匹配造成的。这大致就是MySQL主从同步的原理，真正在其中起到作用的实际上就是这两个日志文件，binlog和中继日志（Relay log）。

## 准备工作：

- 主mysql实例A
- 从mysql实例B
- 尽量保证两个实例在同一内网，且数据库版本一致

### 主库master配置修改：

- 找到主库mysql所在目录的配置文件my.cnf(或者my.ini) 一般在**/etc/my.cnf** 或者**/etc/mysql/my.cnf**修改配置项

```bash
[mysqld]
server-id = 100 #唯一标识 用一整数表示即可
log-bin = mysql-bin #开启binlog日志
```

- 重启mysql,为从库同步主库创建一个用户

```sql
mysql -u root -p
mysql> CREATE USER 'slave1'@'10.0.0.123' IDENTIFIED BY 'slavepassword';#创建用户
mysql> GRANT REPLICATION SLAVE ON *.* TO 'slave1'@'10.0.0.123';#主从复制 授权权限
mysql> flush PRIVILEGES; #刷新权限
```

- 进行锁表(保证数据一致性，加只读锁)

```sql
mysql> flush tables with read lock;
```

- 查看master状态 记录二进制文件名以及POS(例如我这边是msql-bin.000025 637)

```sql
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000025 |      637 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

- 导出数据

```sql
mysqldump -uroot -p --master-data=1 --single-transaction --routines --triggers --events --all-databases > all.sql
```

- 解锁主库

```sql
unlock tables;
```

- 从库全量导入sql数据，保证和主库数据一致

### 从库slave配置修改

- 修改server-id

```bash
[mysqld]
server-id=200 #设置server-id 必须唯一
```

- 重启mysql，打开mysql命令行，执行命令连接master

```sql
mysql> CHANGE MASTER TO
    -> MASTER_HOST='10.0.0.199',
    -> MASTER_USER='slave1',
    -> MASTER_PASSWORD='slavepassword',
    -> MASTER_LOG_FILE='mysql-bin.000025',
    -> MASTER_LOG_POS=637;
```

- 启动slave同步进程

```sql
mysql> start slave;
```

- 查看slave状态

```sql
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 10.0.0.199
                  Master_User: slave1
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000025
          Read_Master_Log_Pos: 637
               Relay_Log_File: centos7-relay-bin.000002
                Relay_Log_Pos: 800
        Relay_Master_Log_File: mysql-bin.000025
             **Slave_IO_Running: Yes
            Slave_SQL_Running: Yes**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 637
              Relay_Log_Space: 975
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 100
                  Master_UUID: 29c8d5f8-2dfe-11eb-996f-0800276c3e95
             Master_Info_File: /www/server/data/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for the slave I/O thread to update it
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
```

当    ****Slave_IO_Running 和 Slave_SQL_Running:都为Yes表示同步设置成功，此时可以在主库修改数据，来看从库是否变更

如果想观察日志都记录了什么咱们可以直接查询对应日志:

```sql
#主库binlog查看 在主库mysql命令行执行如下
show binlog events in  'mysql-bin.000025'; # 后面这个文件名称是文章前面提到查询出来的

#从库relaylog查看 在从库mysql命令行执行如下
show relaylog events in 'centos7-relay-bin.000002'
```

相关参考链接：

- [https://www.jianshu.com/p/0e55e96d1f08](https://www.jianshu.com/p/0e55e96d1f08)

- [https://segmentfault.com/a/1190000022440263](https://segmentfault.com/a/1190000022440263)