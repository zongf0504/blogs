# Mysql 主从--新增从节点
> 对于已经运行一段时间且产生一定的数据的mysql , 在线(不能停止mysql服务)添加从节点还是有点儿麻烦的, 网上也有多种实现方式, 笔者借助于第三方工具Percona innobackupex.因此需要事先在主服务器上安装Percona innobackupex 这个工具.


## 主服务器配置

### 1. 创建二进制目录
* 创建存放二进制日志目录, 用于存放mysql 二进制日志文件
* 修改目录所有者和所属组为mysql, 因为mysql 是以mysql 用户启动的

```bash
[root@gds mysql]# mkdir /var/data/mysql/binlog
[root@gds mysql]# chown mysql:mysql /var/data/mysql/binlog
[root@gds mysql]# drwxr-xr-x. 2 mysql mysql 4096 Aug  4 19:17 /var/data/mysql/binlog/
```

### 2. 创建专门用于主从复制用户
1. 使用管理员账户登录mysql
2. 创建用户主从复制的用户repl, 并设置密码为repl
3. 为repl 用户授权
4. 刷新授权

```bash

>mysql  CREATE USER 'repl'@'%' IDENTIFIED BY 'repl'; 
Query OK, 0 rows affected (0.00 sec)

>mysql GRANT REPLICATION SLAVE ON *.*  TO 'repl'@'%' IDENTIFIED BY 'repl';
Query OK, 0 rows affected, 1 warning (0.00 sec)

>mysql flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

### 3. 修改配置
* 修改mysql配置文件my.cnf ,笔者配置文件位置:  /usr/local/etc/mysql/my.cnf
* 对于二进制文件和二进制索引文件, 默认存放在$datadir目录, 因此相对目录也就是相对于$datadir 目录而言
* 二进制文件命名: 使用_或者驼峰式命名, 不建议使用. 命名, 因为使用多个.会有问题
* 每次重新mysql 都会生成一个新的二进制日志文件, 文件名格式为: mysql_bin_log.000001


```bash
#[mysqld] 节点下

# 指定mysql id, 通常用服务器ip 末位或末6位表示
server_id=100

# 指定mysql 二进制文件, 默认存放在$datadir 目录下
log_bin=logbin/myql_bin_log

# 指定mysql 二进制文件索引
log_bin_index=logbin/mysql_bin_log_index

```

### 4. 重启mysql 服务
* 如果之前没有开启二进制日志,那么需要重启mysql服务, 否则不需要重启

```bash
[root@localhost mysql]# service mysql restart
Shutting down MySQL.... SUCCESS! 
Starting MySQL. SUCCESS! 
```

* 查看二进制文件
```bash
[root@localhost mysql]# ls /var/data/mysql/binlog/
mysql_bin_log.000001  mysql_bin_log_index.index
```

* 可登录主服务器查看主服务器状态:
```bash
mysql> show master status;
+----------------------+----------+--------------+------------------+-------------------+
| File                 | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+----------------------+----------+--------------+------------------+-------------------+
| mysql_bin_log.000001 |    23742 |              |                  |                   |
+----------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```


### 5. 对主库做数据库快照
* 在mysql 主节点上, 使用innobackupex 命令对数据库做全库数据快照, 并记录二进制日志偏移量
* 如果不是本地mysql的话, 可以只通过--host, --port 指定数据库位置
* 笔者使用配置文件方式: 指定my.cnf 位置, 用户名, 密码, 数据库, 和快照存放路径, 生成一个文件名为时间戳的文件夹
* 注意生成数据快照之后, 主数据库就不能进行重启操作, 因为每次重启都会产生一个新的二进制日志文件

```bash
[root@localhost mysql]# innobackupex --defaults-file=/usr/local/etc/mysql/my.cnf --user=root --password=root --database=zabbix /tmp/percona
...
xtrabackup: Transaction log of lsn (393631383) to (393651645) was copied.
170804 19:26:15 completed OK!

[root@localhost mysql]# ls /tmp/percona
2017-08-04_19-26-07
```

### 6. 保证数据一致性
* 生成备份数据和二进制日志的对应关系, 记录在xtrabackup_binlog_info文件中

```bash
[root@localhost percona]# innobackupex --user=root --password=root --apply-log /tmp/percona/2017-08-04_19-26-07/
...
InnoDB: Shutdown completed; log sequence number 393652264
170804 19:27:37 completed OK!

[root@localhost percona]#  cat /tmp/percona/2017-08-04_19-26-07/xtrabackup_binlog_info 
mysql_bin_log.000001    105847

```

### 7. 压缩备份文件
* 压缩日志文件, 方便从服务器之间数据传输

```bash
[root@localhost percona]# tar -zcf mysql.tar.gz 2017-08-04_19-26-07/
[root@localhost percona]# ls
2017-08-04_19-26-07  mysql.tar.gz
```

## 2. 从服务器
* 如果从服务器为新装mysql服务器, 可不进行初始化登录操作, 直接制定数据目录, 迁移数据即可
* 从服务器也需要创建二进制目录, 并修改所属组和所有者, 此步骤同主服务器

### 1. 停止从服务器
* 如果没启动或新装mysql 可省略此步骤

```bash
[root@localhost percona]# service mysql stop
```

### 2. 修改从服务器配置
* 修改配置文件, my.cnf, 建议除了server_id 不一样外, 其它主要配置和主节点保持一致
* 从服务器可设置为只读

```bash
#[mysqld] 节点下

# 指定mysql id, 通常用服务器ip 末位或末6位表示
server_id=100

# 指定mysql 二进制文件, 默认存放在$datadir 目录下
log_bin=logbin/myql_bin_log

# 指定mysql 二进制文件索引
log_bin_index=logbin/mysql_bin_log_index

```

### 3. 下载主服务器备份文件
* 下载备份文件到/tmp/percona 目录
* 解压备份文件

```bash
[root@gds percona]# scp admin@172.22.12.225:/tmp/percona/mysql.tar.gz .
admin@172.22.12.225's password: 
mysql.tar.gz                                                      100%   65MB  10.9MB/s   00:06    
[root@gds percona]# tar -zxf mysql.tar.gz 
[root@gds percona]# ls
2017-08-04_19-26-07  mysql.tar.gz
```

### 4. 完全恢复从服务器数据
1. 清空或备份mysql 数据目录
2. 将备份文件夹所有数据, 拷贝到数据目录


```bash
[root@gds mysql]# rm -rf /var/data/mysql/*
[root@gds mysql]# mv /tmp/percona/2017-08-04_19-26-07/* /var/data/mysql/
```

### 5. 创建二进制日志存放目录
* 创建二进制日志文件存放目录并修改所有者和所属组

```bash
[root@gds mysql]# mkdir /var/data/mysql/binlog

```

### 6. 修改mysql 数据目录所有者所属组

```bash
[root@gds mysql]# chown -R mysql:mysql /var/data/mysql
```

### 7. 启动从服务器
```
[root@gds percona]# service mysql start
Starting MySQL. SUCCESS! 
```

### 8. 查看二进制日志信息
* 查看日志文件名和日志偏移量,  用于设置主节点时参数mster_log_file 和  mster_log_pos

```bash
[root@gds percona]# cat /var/data/mysql/xtrabackup_binlog_info 
mysql_bin_log.000001    105847
```

### 9. 从服务器设置主节点
1. 使用root账号登录, mysql 服务器
2. 设置从节点的主节点信息
3. 开启从服务同步

```
>mysql change master to master_host='192.168.1.100',master_user='repl',master_password='repl',master_log_file='mysql_bin_log.000001',master_log_pos=105847; 


```

### 10. 启动从服务

```bash
mysql> start slave;
Query OK, 0 rows affected (0.05 sec)
```

#### 7. 查看从节点状态

```bash
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 172.22.12.225
                  Master_User: repl
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql_bin_log.000002
          Read_Master_Log_Pos: 120321112
               Relay_Log_File: gds-relay-bin.000002
                Relay_Log_Pos: 40840
        Relay_Master_Log_File: mysql_bin_log.000002
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 120198962
              Relay_Log_Space: 163195
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 249
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 225
                  Master_UUID: 92e53267-7386-11e7-985d-14b31f053816
             Master_Info_File: /var/data/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Reading event from the relay log
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.00 sec) 
``` 




























