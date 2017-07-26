# LNMP 环境搭建
> 所谓的LNMP 环境指的是在Linux 系统上搭建, Nginx, Mysql, PHP 的环境. 这套环境搭建起来并不是太简单, 因为每一个环境都依赖其它一些包或软件. 笔者采用yum 方式安装依赖和mysql 环境, 源码安装nginx, php 环境.


# 1. 安装介绍

## 1.1 环境介绍
笔者安装环境:
* Linux: centos 6.8 x64
* Nginx: nginx-1.11.13.tar.gz
* Mysql: mysql57-community-release-el6-9.noarch.rpm
* PHP: php-7.1.7.tar.bz2

## 1.2 安装目录 
* 以下这些目录需要手工创建

| 软件 | 目录 | 用途 |
| :--- | :--- | :---|
| Nginx | /usr/local/src/nginx | nginx 源码目录 |
| Nginx | /usr/local/src/nginx/modules | nginx 插件目录
| Nginx | /var/logs/nginx | nginx 日志目录 |
| Nginx | /var/data/nginx | nginx 数据目录 |
| Nginx | /var/run/nginx | nginx 进程id文件等目录 |
| Nginx | /var/data/cache/nginx | nginx 缓存数据目录 | 
| Mysql | /var/data/mysql | mysql 数据库数据存放目录 |
| Mysql | /var/logs/mysql | mysql 日志目录  |
| Mysql | /var/run/mysql | mysql 运行进程相关文件 |



# 2. Nginx 安装

## 2.1 安装依赖环境

```bash
[root@localhost nginx]# yum -y install gcc gcc-c++ zlib zlib-devel openssl openssl-devel pcre-devel
```

## 2.1 安装Nginx

### 2.1.1 解压nginx
* 解压nginx压缩包, 并切换到nginx 目录: cd /usr/local/src/nginx/nginx-1.11.13

### 2.1.2 创建nginx 用户

```bash
[root@localhost nginx]# useradd -s /sbin/nologin nginx
[root@localhost nginx]# id nginx
uid=505(nginx) gid=506(nginx) groups=506(nginx)
```

### 2.1.3 自定义配置
```bash
[root@localhost nginx-1.11.3]# pwd
/usr/local/src/nginx/nginx-1.11.3
[root@localhost nginx-1.11.3]# ls ../modules/
ngx-fancyindex
[root@gds nginx-1.11.3]# ./configure \
--prefix=/var/data/nginx \
--sbin-path=/usr/sbin/nginx \
--conf-path=/etc/.nginx/nginx.conf \
--error-log-path=/var/logs/nginx/error.log \
--http-log-path=/var/logs/nginx/access.log \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/run/nginx/nginx.lock \
--http-client-body-temp-path=/var/data/cache/nginx/client_temp \
--http-proxy-temp-path=/var/data/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/data/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/data/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/data/cache/nginx/scgi_temp \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-stream_ssl_module \
--with-http_slice_module \
--with-http_stub_status_module \
--with-http_perl_module \
--add-module=../modules/ngx-fancyindex/
```

### 2.1.4 编译
```bash
[root@localhost nginx-1.11.13]# make
```

### 2.1.5 安装
```bash
[root@localhost nginx-1.11.13]# make install
```

### 2.1.6 测试
* 查看nginx 版本号和安装信息

```bash
[root@localhost nginx-1.11.3]# nginx -v
nginx version: nginx/1.11.3
[root@localhost nginx-1.11.3]# nginx -V
nginx version: nginx/1.11.3
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-18) (GCC) 
built with OpenSSL 1.0.1e-fips 11 Feb 2013
TLS SNI support enabled
configure arguments: --prefix=/var/data/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/.nginx/nginx.conf --error-log-path=/var/logs/nginx/error.log --http-log-path=/var/logs/nginx/access.log --pid-path=/var/run/nginx/nginx.pid --lock-path=/var/run/nginx/nginx.lock --http-client-body-temp-path=/var/data/cache/nginx/client_temp --http-proxy-temp-path=/var/data/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/data/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/data/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/data/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_gunzip_module --with-http_gzip_static_module --with-stream_ssl_module --with-http_slice_module --with-http_stub_status_module --with-http_perl_module
```

## 2. Mysql 环境安装
* 笔者操作系统为CentOS 6.8, 如果使用yum 安装mysql, 那么安装的是mysql 5.5 版本的, 而mysql 这个mysql 版本有点儿老, 笔者选择安装mysql 5.7.

### 2.1 下载mysql.5.7 yum 源配置

```bash
[root@localhost mysql]# wget https://dev.mysql.com/get/mysql57-community-release-el6-9.noarch.rpm
--2017-07-26 13:57:39--  https://dev.mysql.com/get/mysql57-community-release-el6-9.noarch.rpm
Connecting to 172.17.18.84:8080... connected.
Proxy request sent, awaiting response... 302 Found
Location: https://repo.mysql.com//mysql57-community-release-el6-9.noarch.rpm [following]
--2017-07-26 13:57:40--  https://repo.mysql.com//mysql57-community-release-el6-9.noarch.rpm
Connecting to 172.17.18.84:8080... connected.
Proxy request sent, awaiting response... 200 OK
Length: 9216 (9.0K) [application/x-redhat-package-manager]
Saving to: “mysql57-community-release-el6-9.noarch.rpm”

100%[==================================>] 9,216       --.-K/s   in 0s      

2017-07-26 13:57:41 (1.36 GB/s) - “mysql57-community-release-el6-9.noarch.rpm” saved [9216/9216]

[root@localhost mysql]# ls
mysql57-community-release-el6-9.noarch.rpm

```

### 2.2 设置本地yum源
* 将mysql 5.7 的yum源地址安装在本地
* 命令: rpm -Uvh mysql57-community-release-el6-9.noarch.rpm 或者 yum localinstall mysql57-community-release-el6-9.noarch.rpm 

```bash
[root@localhost mysql]# rpm -Uvh mysql57-community-release-el6-9.noarch.rpm
warning: mysql57-community-release-el6-9.noarch.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
Preparing...                                                            (100########################################### [100%]
   1:mysql57-community-relea                                            ( 65########################################### [100%]
[root@localhost mysql]# ls /etc/yum.repos.d/mysql*
/etc/yum.repos.d/mysql-community.repo
/etc/yum.repos.d/mysql-community-source.repo   

```

### 2.3 安装mysql

```bash
[root@localhost mysql]# yum -y install mysql-community-server mysql-devel

```

### 2.4 初始化启动mysql

```bash
[root@localhost mysql]# service mysqld start  
Initializing MySQL database:                               [  OK  ]
Starting mysqld:                                           [  OK  ]
[root@gds mysql]# service mysqld stop
Stopping mysqld:                                           [  OK  ]
```

### 2.5 初始化mysql 配置
* 修改数据库默认编码为: utf-8
* 修改日志,数据,运行id 等目录位置
* 关闭密码强度校验: mysql 5.7在设置密码时, 出于安全考虑新增了密码校验, 建议再生产环境使用, 但是在开发环境就没有必要了, 可以选择关闭. 
mysql 默认密码校验规则: 由大写字母, 小写字母, 数字, 特殊符合组成的至少8位的密码

修改: /etc/my.cnf
```bash

#数据库编码: mysqld 下添加一行
[mysqld]
#指定数据库默认编码
character_set_server = utf8

# 关闭密码校验
validate_password=off

#指定客户端连接默认编码
[client]
default-character-set = utf8


character_set_server = utf8
```

### 2.6 查看root 初始密码
* 密码为: +)j8KEtxGNUV

```bash
[root@gds mysql]# grep 'temporary password' /var/log/mysqld.log
2017-07-26T08:04:47.499031Z 1 [Note] A temporary password is generated for root@localhost: +)j8KEtxGNUV
```

### 2.7 登录客户端, 修改root 密码
* 使用mysql 客户端连接mysql数据库: mysql -u 用户名 -p
* 修改root用户名, 我们设置一个简单的密码: root
* 默认情况下root用户不允许从其他电脑登录, 我们授权允许从任何机器上登录

```bash
[root@gds mysql]# mysql -u root -p 
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.19

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> ALTER USER root@localhost IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.04 sec)

mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;       
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

### 2.7 查看数据库编码

```bash
mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```

## 3. PHP 环境安装 


























