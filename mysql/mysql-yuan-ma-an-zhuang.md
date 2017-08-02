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


### 1. 下载mysql 源码
* 下载地址: 

### 2. 创建mysql 用户
* 创建mysql 用户

```bash
[root@localhost ~]# useradd -s /sbin/nologin mysql
[root@localhost ~]# id mysql
```

### 3. 创建相关文件

```bash
[root@localhost ~]# mkdir -p /var/data/mysql /var/logs/mysql /usr/local/mysql /var/run/mysql /usr/local/etc/mysql
[root@localhost ~]# chown mysql:mysql /var/data/mysql /var/logs/mysql /usr/local/mysql /var/run/mysql /usr/local/etc/mysql

```

### 4. 安装mysql 依赖

```bash
[root@localhost ~]# yum -y install make gcc gcc-c++ cmake gcc-g77 flex bison file libtool libtool-libs autoconf kernel-devel gd gd-devel libxml2 libxml2-devel glib2 glib2-devel bzip2 bzip2-devel libevent libevent-devel ncurses ncurses-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel gettext gettext-devel gmp-devel pspell-devel unzip libcaplsof 

```

## 2. 安装mysql

### 2.1 解压文件

```bash
[root@localhost mysql]# tar -zxf mysql-5.7.19.tar.gz -C /usr/local/mysql
```

### 2.2 安装boost
```bash
[root@localhost mysql-5.7.19]# cd /usr/local/mysql/mysql-5.7.19
[root@localhost mysql-5.7.19]# cmake -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/usr/local/boost
```

### 2.3 设置编译环境

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
* mysql 编译的过程会比较长, 具体依机器性能决定, 笔者花了半个多小时

```bash
[root@localhost mysql-5.7.19]# make
```

### 2.5 安装

```bash
[root@localhost mysql-5.7.19]# make install
````

## 3. 配置mysql


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
# 设置客户端连接默认编码
default-character-set = utf8
# 设置客户端连接sock文件位置
socket=/usr/local/mysql/mysql.sock

```











