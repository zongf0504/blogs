# Zabbix 环境搭建
> Zabbix 是一个分布式服务器监控系统, 安装依赖于LNMP 或 LAMP 环境, 笔者选择的是LNMP. LNMP的搭建不在本篇讨论范围之内.

## 1.1 环境依赖:

### 1.1 环境介绍
* Linux: centos 6.8 x64
* Nginx: nginx-1.11.13.tar.gz
* Mysql: mysql57-community-release-el6-9.noarch.rpm
* PHP: php-7.1.7.tar.bz2
* Zabbix: zabbix-3.0.10.tar.gz

### 1.2 安装目录
| 软件 | 目录 | 用途 |
| :--- | :--- | :---|
| Zabbix| /usr/local/src/zabbix| zabbix 源码目录 |
| Zabbix| /etc/.zabbix| zabbix 配置目录 |


## 2. 安装Zabbix

### 2.1 新建用户
* 新建zabbix 用户

```bash
[root@localhost zabbix]# useradd -s /sbin/nologin zabbix
[root@localhost zabbix]# id zabbix[root@gds php-5.6.30]# id zabbix
uid=506(zabbix) gid=507(zabbix) groups=507(zabbix)
```

### 2.2 配置路径
* 进入zabbix 源码目录, 执行配置命令
* 安装server和agent

```bash
[root@gds zabbix-3.0.10]# ./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2

```

### 2.3 安装
```bash
[root@gds zabbix-3.0.10]# make install
```


## 3. 整合zabbix 和 nginx
### 3.1 拷贝zabbix 页面文件

```bash
[root@gds zabbix-3.0.10]# cp -r /usr/local/src/zabbix/zabbix-3.0.10/frontends/php /var/data/nginx/php/zabbix
```

### 3.2 配置nginx
* nginx 添加zabbix 配置

```bash
#php 反向代理配置
location ~ \.php$ {
    root           /var/data/nginx/php;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
#zabbix 配置
location /zabbix {
    root        /var/data/nginx/php/nginx;
}
```

## 4. 整合zabbix 和 mysql

### 4.1 mysql 中创建用户导入数据
* 登录mysql 客户端
* 创建数据库zabbix
* 创建导入数据

```bash
#创建数据库
mysql> CREATE DATABASE zabbix DEFAULT CHARACTER SET utf8 ;                
Query OK, 1 row affected (0.00 sec)
mysql> use zabbix;
Database changed

#导入数据
mysql> source /usr/local/src/zabbix/zabbix-3.0.10/database/mysql/schema.sql;
mysql> source /usr/local/src/zabbix/zabbix-3.0.10/database/mysql/data.sql;
mysql> source /usr/local/src/zabbix/zabbix-3.0.10/database/mysql/images.sql;

#创建用户
mysql> CREATE USER 'zabbix@%' IDENTIFIED BY 'zabbix';
Query OK, 0 rows affected (0.00 sec)
mysql> GRANT ALL PRIVILEGES ON *.* TO 'zabbix'@'%' IDENTIFIED BY 'zabbix' WITH GRANT OPTION; 
Query OK, 0 rows affected, 1 warning (0.00 sec)

```

### 4.2 配置mysql
* 修改配置文件: /usr/local/etc/zabbix_server.conf , 修改以下配置
```bash
DBHost=127.0.0.1
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
DBSocket=/var/lib/mysql/mysql.sock
DBPort=3306
Timeout=4
LogSlowQueries=3000
```

### 4.3 配置php
* 设置php.ini: vim /usr/local/etc/php.ini

```bash
post_max_size = 16M
max_execution_time = 300
max_input_time = 300
date.timezone = PRC
enable_post_data_reading = Off
always_populate_raw_post_data = -1
mbstring.func_overload = 0
extension=php_gd2.dll
extension=php_gettext.dll
```


## 5. 测试启动

### 5.1 依次启动环境
* 启动mysql: service mysqld restart
* 启动php:  php-fpm 
* 启动nginx: nginx -s reload
* 启动zabbix: zabbix_server

### 5.2 浏览器中访问zabbix
* 访问地址: http://localhost/zabbix/setup.php




















