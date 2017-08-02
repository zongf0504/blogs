# Mysql 源码安装
> Linux 下安装mysql 可以选择rpm 安装也可以选择源码安装, 源码安装比较复杂, 但是源码安装更加灵活, 性能更高. 所以还是推荐使用源码安装.笔者安装环境: Centos 6.8, mysql 5.7


## 1. 安装准备工作

|  安装目录 | 说明 |
| :--- | :--- |
| /usr/local/mysql | Mysql 文件安装目录 |
| /var/data/mysql | Mysql 数据存放目录 |
| /var/logs/mysql | Mysql 日志存放目录 |
| /var/run/mysql | Myql pid 存放目录 |
| /usr/local/etc/mysql | Mysql 配置文件存放目录 |
| /usr/local/src/mysql | Mysql 源码存放目录 |
| /usr/local/boost | boost 模板安装目录 |

### 1. 下载mysql 源码
* 下载地址: [mysql download](https://dev.mysql.com/downloads/mysql/)

### 2. 创建mysql 用户
* 创建mysql 用户, 不允许mysql 用户登录

```bash
[root@localhost ~]# useradd -s /sbin/nologin mysql
[root@localhost ~]# id mysql
uid=505(mysql) gid=505(mysql) groups=505(mysql)
```

### 3. 创建相关目录
* 创建mysql存储的相关目录, 并指定所有者,所属组

```bash
[root@localhost ~]# mkdir -p /var/data/mysql /var/logs/mysql /usr/local/mysql /var/run/mysql /usr/local/etc/mysql
[root@localhost ~]# chown mysql:mysql /var/data/mysql /var/logs/mysql /usr/local/mysql /var/run/mysql /usr/local/etc/mysql

```

### 4. 安装mysql 依赖
* mysql 依赖有点儿多, 使用yum 安装就好了

```bash
[root@localhost ~]# yum -y install make gcc gcc-c++ cmake gcc-g77 flex bison file libtool libtool-libs autoconf kernel-devel gd gd-devel libxml2 libxml2-devel glib2 glib2-devel bzip2 bzip2-devel libevent libevent-devel ncurses ncurses-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel gettext gettext-devel gmp-devel pspell-devel unzip libcaplsof 

```

## 2. 安装mysql

### 2.1 解压文件
* 将mysql 源码压缩包解压到 /usr/local/src/mysql 目录下

```bash
[root@localhost mysql]# tar -zxf mysql-5.7.19.tar.gz -C /usr/local/src/mysql
```

### 2.2 安装boost
* mysql 安装过程中需要使用boost 模板, 所以需要事先安装一下.此过程有点儿慢
* 注意需要进入mysql 源码目录内执行

```bash
[root@localhost mysql-5.7.19]# cd /usr/local/src/mysql/mysql-5.7.19
[root@localhost mysql-5.7.19]# cmake -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/usr/local/boost
```

### 2.3 设置编译环境
* mysql 使用cmake 命令安装, 设置安装目录, 数据存放目录 , 日志目录等环境
* 注意需要在mysql 源码目录内执行

```bash
[root@localhost mysql-5.7.19]# cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/var/data/mysql/ \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DSYSCONFDIR=/usr/local/etc/mysql \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DEXTRA_CHARSETS=all \
-DWITH_SSL=system \
-DMYSQL_TCP_PORT=3306 \
-DWITH_SSL=bundled \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=/usr/local/boost \
```

### 2.4 编译
* mysql 编译的过程会比较长, 具体依机器性能决定, 笔者物理机上花了半个小时左右
* 注意需要在mysql 源码目录内执行

```bash
[root@localhost mysql-5.7.19]# make
```

### 2.5 安装
* 注意需要在mysql 源码目录内执行

```bash
[root@localhost mysql-5.7.19]# make install
````

### 2.6 安装linux 服务
* 将mysql.server 拷贝到/etc/init.d 目录下, 并更名为mysql, 则可以使用service 命令进行管理mysql 服务了

```bash
[root@localhost mysql-5.7.19]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql

```

### 2.7 添加环境变量
1. 编辑配置文件: vim /etc/profile
2. 使配置立即生效: source /etc/profile

``` bash
#set mysql
export MYSQL_HOME=/usr/local/mysql
export PATH=$PATH:$MYSQL_HOME/bin
```

### 2.8 设置libmysqlclient
* 查看mysql lib 目录是否有libmysqlclient.so 文件, 有的话执行下面命令

```bash
[root@localhost mysql]# ls /usr/local/mysql/lib/libmysqlclient.so.*
/usr/local/mysql/lib/libmysqlclient.so.20
/usr/local/mysql/lib/libmysqlclient.so.20.3.6
[root@localhost mysql]# echo "/usr/local/mysql/lib" >> /etc/ld.so.conf
[root@localhost mysql]# ldconfig
```


## 3. 配置mysql
* 需自己在/usr/local/etc/mysql 目录下创建 my.cnf 文件
* 文件内容如下: vim /usr/local/etc/mysql/my.cnf

```bash
[mysqld]
# 指定mysql 数据存放目录
datadir=/var/data/mysql
# 指定mysql 通信文件
socket=/usr/local/mysql/mysql.sock
# 指定mysql 日志
log-error=/var/logs/mysql/mysqld.log
# 指定mysql pid 存放位置
pid-file=/var/run/mysql/mysqld.pid
# 指定数据库密码
character_set_server = utf8

symbolic-links=0


[client]
# 设置客户端连接默认编码
default-character-set = utf8
# 设置客户端连接sock文件位置
socket=/usr/local/mysql/mysql.sock

[mysqladmin]
# 设置连接默认编码
default-character-set = utf8
# 设置连接sock文件位置
socket=/usr/local/mysql/mysql.sock

```

## 4. 数据库初始化

### 4.1 初始化数据库
* 初始化数据库的过程会为root 用户产生一个随机密码
* /var/data/mysql 目录必须是空的, 且mysql 具有读写权限

```bash
[root@localhost mysql-5.7.19]# mysqld --initialize --user=mysql
```

### 4.2 查看root 登录密码
* 初始化数据时, 日志会输出到/var/logs/mysql/mysqld.log 文件中

```bash
[root@localhost mysql-5.7.19]# cat /var/logs/mysql/mysqld.log | grep password

```

## 5. 启动数据库
```
[root@localhost mysql-5.7.19]# service mysql start
```

## 6. 客户端连接
### 6.1 使用mysql 客户端连接数据库
* 使用mysql 客户端连接mysql数据库: mysql -u 用户名 -p

```bash
[root@gds mysql]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.19

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```

### 6.2 修改root 密码
* 修改root用户名, 我们设置一个简单的密码: root

```mysql
mysql> ALTER USER root@localhost IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.04 sec)

```

### 6.3 授予允许远程访问root 权限
* 默认情况下root用户不允许从其他电脑登录, 我们授权允许从任何机器上登录


```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)


```

### 6.4 查看数据库编码

```bash
mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name | Value |
+--------------------------+----------------------------+
| character_set_client | utf8 |
| character_set_connection | utf8 |
| character_set_database | utf8 |
| character_set_filesystem | binary |
| character_set_results | utf8 |
| character_set_server | utf8 |
| character_set_system | utf8 |
| character_sets_dir | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)

```









