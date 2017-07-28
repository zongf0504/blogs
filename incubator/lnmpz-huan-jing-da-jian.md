# LNMP 环境搭建
> 所谓的LNMP 环境指的是在Linux 系统上搭建, Nginx, Mysql, PHP 的环境. 这套环境搭建起来并不是太简单, 因为每一个环境都依赖其它一些包或软件. 笔者采用yum 方式安装依赖, 源码安装nginx, php, mysql 环境.


# 1. 安装介绍

## 1.1 环境介绍
笔者安装环境:
* Linux: centos 6.8 x64
* Nginx: nginx-1.11.13.tar.gz
* Mysql: mysql57-community-release-el6-9.noarch.rpm
* PHP: php-7.1.7.tar.bz2

## 1.2 安装目录 
* 以下这些目录需要手工创建

| 软件 | 目录 | 用途 | 所有者 |
| :--- | :--- | :---| :--- |
| Nginx | /usr/local/src/nginx | nginx 源码目录 | root |
| Nginx | /usr/local/src/nginx/modules | nginx 插件目录| root |
| Nginx | /var/logs/nginx | nginx 日志目录 | nginx |
| Nginx | /var/data/nginx | nginx 数据目录 | nginx |
| Nginx | /var/run/nginx | nginx 进程id文件等目录 | nginx |
| Nginx | /var/data/cache/nginx | nginx 缓存数据目录 | nginx |
| Mysql | /var/data/mysql | mysql 数据目录 | mysql |
| Mysql | /usr/local/src/mysql | mysql 源代码目录 | root |
| Mysql | /usr/local/mysql | mysql 安装目录 | mysql |
| Mysql | /usr/logs/mysql | mysql 日志目录 | mysql |
| PHP | /usr/local/src/php | php 源码目录 | root |
| PHP | /usr/local/php | php 安装目录 | nginx |
| PHP | /usr/local/etc/php | php 配置目录 | nginx |
| Zabbix | /usr/local/src/zabbix | zabbix 源代码目录 | nginx |
| Zabbix | /usr/local/zabbix | zabbix 安装目录 | root |
| Zabbix | /usr/local/etc/zabbix | zbbix 配置文件目录 | zabbix |
| Zabbix | /usr/logs/zabbix | zabbix 日志目录 | zabbix |
| Zabbix | /usr/run/zabbix | zabbix 进程id 目录 | zabbix |

## 1.3 设置epel 源
* 有些软件依赖centos 默认的yum 源中没有, 需要从epel 源中下载

### 1.3.1 安装
```bash
[root@localhost ~]# yum install yum-priorities
[root@localhost ~]# rpm -Uvh http://mirrors.ustc.edu.cn/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
[root@localhost ~]# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
[root@localhost ~]# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
```

### 1.3.2 修改优先级
* 修改数据源的优先级为优先去yum的官方地址下载,找不到则去epel源下载
* 配置文件/etc/yum.repos.d/epel.repo 中, [epel] 节点下添加属性: priority=11

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

# 3. Mysql 环境安装
* 笔者操作系统为CentOS 6.8, 如果使用yum 安装mysql, 那么安装的是mysql 5.5 版本的, 而mysql 这个mysql 版本有点儿老, 笔者选择安装mysql 5.7.
* 笔者采用源码编译方式安装mysql, 这个过程比较久, 具体情况根据机器性能而定

## 3.1 安装准备
### 3.1.1 创建mysql 用户
```bash
[root@localhost ~]# useradd -s /sbin/nologin mysql
[root@localhost ~]# id mysql
```

### 3.1.2 创建mysql 相关目录
```
[root@localhost ~]# mkdir -p /var/data/mysql /var/logs/mysql /usr/local/mysql /var/run/mysql /usr/local/etc/mysql
[root@localhost ~]# chown mysql:mysql /var/data/mysql /var/logs/mysql /usr/local/mysql /var/run/mysql /usr/local/etc/mysql
```

### 3.1.2 安装mysql 依赖
* mysql 依赖的包有点儿多, 需要些时间

```bash
[root@localhost ~]# yum -y install make gcc gcc-c++ cmake gcc-g77 flex bison file libtool libtool-libs autoconf kernel-devel gd gd-devel libxml2 libxml2-devel glib2 glib2-devel bzip2 bzip2-devel libevent libevent-devel ncurses ncurses-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel gettext gettext-devel gmp-devel pspell-devel unzip libcaplsof 
```

## 3.3 编译mysql
* 进入mysql 目录, 进行编译安装

### 3.3.1 安装cmake boost
* 编译mysql 需要用到boost , 把它安装在/usrl/local/boost 目录

```bash
[root@localhost mysql-5.7.19]# cd /usr/local/src/mysql/mysql-5.7.19
[root@localhost mysql-5.7.19]# cmake -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/usr/local/boost
```

### 3.3.2 配置mysql 安装路径
* 如果配置出现错误, 需要重新执行此命令时, 需要删除当前目录下才: CMakeCache.txt 文件
* 需指定刚刚安装的boost 路径

```bash
[root@localhost mysql-5.7.19]# cmake  \
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

### 3.3.3 编译mysql 
* mysql 编译的过程会比较长, 具体依机器性能决定, 笔者花了半个多小时

```bash
[root@localhost mysql-5.7.19]# make
```

### 3.3.4 安装mysql

```bash
[root@localhost mysql-5.7.19]# make install
````

#### 3.3.5 新建mysql配置文件: /usr/local/etc/mysql/my.cnf
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
```

### 3.3.5 安装mysql 服务
* centos 可以将mysql.server 脚本拷贝到/etc/init.d 目录中, 那么以后就可以使用service 命令启动了

```bash
[root@localhost mysql-5.7.19]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

### 3.3.6 添加环境变量
* 编辑配置文件: vim /etc/profile
``` bash
#set mysql
export MYSQL_HOME=/usr/local/mysql
export PATH=$PATH:$MYSQL_HOME/bin
```

* 使配置立即生效 
```bash
[root@localhost mysql-5.7.19]# source /etc/profile
```

### 3.3.7 初始化mysql 数据库
* 初始化数据库的过程会为root 用户产生一个随机密码
* /var/data/mysql 目录必须是空的, 且mysql 具有读写权限

```bash
[root@localhost mysql-5.7.19]# mysqld --initialize --user=mysql
```

### 3.3.8 查看root 随机密码
* 初始化数据时, 日志会输出到/var/logs/mysql/mysqld.log 文件中

```bash
[root@localhost mysql-5.7.19]# cat /var/logs/mysql/mysqld.log | grep password

```

### 3.3.8 启动mysql 服务
* 安装服务之后, 就可以使用service 命令进行管理mysql 服务器了

```
[root@localhost mysql-5.7.19]# service mysql start
```

### 3.3.9 登录客户端, 修改root 密码
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

### 3.3.10 查看数据库编码

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

mysql> exit
Bye

```

### 3.3.11 设置libmysqlclient
* 查看mysql lib 目录是否有libmysqlclient.so 文件, 有的话执行下面命令

```bash
[root@localhost mysql]# echo "/usr/local/mysql/lib" >> /etc/ld.so.conf
[root@localhost mysql]# ldconfig
```


# 4. PHP 环境安装 
* 笔者并不懂PHP 开发, 因此只能从网上收集资料,比葫芦画瓢安装, 并不太清楚各个配置是什么意思.
* php 版本选择: php-5.6.30.tar.bz2

## 4.1 安装依赖:

### 4.1.1 yum 安装依赖
```bash
[root@localhost mysql]# yum -y install libxml2 libXpm-devel libxml2-devel libjpeg-devel libpng-devel bzip2-devel libcurl-devel freetype freetype-devel libxslt-devel net-snmp-devel net-snmp perl-DBI php-gd php-bcmath php-mbstring gd libmcrypt openldap openldap-devel libjpeg libjpeg-devel libpng libpng-devel libpng10 libpng10-devel  php-mcrypt  libmcrypt  libmcrypt-devel readline readline-devel
```

### 4.1.2 源码安装jpeg 环境
安装jpeg依赖:
```bash
[root@localhost php]# wget http://www.ijg.org/files/jpegsrc.v9b.tar.gz
[root@localhost php]# tar -zxf jpegsrc.v9b.tar.gz
[root@localhost php]# cd jpeg-9b
[root@localhost jpeg-9b]# ./configure --prefix=/usr/local/jpeg --enable-shared --enable-static
[root@localhost jpeg-9b]# make && makeinstall

```

### 4.1.2 设置ldap 包
* 由于笔者是64位操作系统, 所以需要将ldap 相关库文件拷贝到/usr/lib 目录下 

```bash
[root@localhost php-5.6.30]# cp /usr/lib64/libldap* /usr/lib/
```

### 4.1.3 创建目录:
* 
```bash
[root@localhost php-5.6.30]# mkdir -p /usr/local/etc/php  /var/data/nginx/php
[root@localhost php-5.6.30]# chown nginx:nginx /usr/local/etc/php /var/data/nginx/php
```


## 4.2  解压php 
```bash
[root@localhost php]# tar -jxf php-5.6.30.tar.bz2 
[root@localhost php]# ls
php-5.6.30
```

### 4.2.1 配置安装环境

```bash
[root@localhost php-5.6.30]# pwd
/usr/local/src/php/php-5.6.30
[root@localhost php-5.6.30]# 
./configure \
--prefix=/usr/local/php \
--with-config-file-path=/usr/local/etc/php \
--with-mysql=/usr/local/mysql \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-mysql-sock=/usr/local/mysql/mysql.sock \
--with-fpm-user=nginx \
--with-fpm-group=nginx \
--enable-inline-optimization \
--enable-fpm \
--enable-soap \
--enable-pcntl \
--enable-xml \
--with-libxml-dir \
--with-xmlrpc \
--with-openssl \
--with-mcrypt \
--with-mhash \
--with-pcre-regex \
--with-sqlite3 \
--with-zlib \
--enable-bcmath \
--with-iconv \
--with-bz2 \
--enable-calendar \
--with-curl \
--with-cdb \
--enable-dom \
--enable-exif \
--enable-fileinfo \
--enable-filter \
--with-pcre-dir \
--enable-ftp \
--with-gd \
--with-openssl-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib-dir  \
--with-freetype-dir \
--enable-gd-native-ttf \
--with-gettext \
--with-mhash \
--enable-json \
--enable-mbstring \
--disable-mbregex \
--disable-mbregex-backtrack \
--with-libmbfl \
--with-onig \
--enable-pdo \
--with-pdo-mysql \
--with-zlib-dir \
--with-pdo-sqlite \
--with-readline \
--enable-session \
--enable-shmop \
--enable-simplexml \
--enable-sockets \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-wddx \
--with-libxml-dir  \
--with-xsl \
--enable-zip \
--enable-mysqlnd-compression-support \
--with-pear \
--with-ldap \

```

### 4.2.2 编译
```bash
[root@localhost php-5.6.30]# make
```

### 4.2.3 安装
```bash
[root@localhost php-5.6.30]# make install 
```

### 4.2.4 拷贝配置文件
```bash
[root@localhost php-5.6.30]# cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
[root@localhost php-5.6.30]# cp ./php.ini-development /usr/local/etc/php/php.ini
```

### 4.2.5 添加环境变量
* 编辑文件: vim /etc/profile

```bash
#php env
export PHP_HOME=/usr/local/php
export PATH=$PATH:$PHP_HOME/bin:$PHP_HOME/sbin	
```

### 4.2.6 使配置立即生效
```bash
[root@localhost php]# source /etc/profile
```

## 4.3 查看php 
```bash
[root@localhost php-5.6.30]# php-fpm -v
PHP 5.6.30 (fpm-fcgi) (built: Jul 27 2017 09:55:34)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
```



# 5. 安装Zabbix

## 5.1 安装准备

### 5.1.1 新建用户
* 新建zabbix 用户

```bash
[root@localhost zabbix]# useradd -s /sbin/nologin zabbix
[root@localhost zabbix]# id zabbix[root@gds php-5.6.30]# id zabbix
uid=506(zabbix) gid=507(zabbix) groups=507(zabbix)
```

## 5.2 安装

### 5.2.1 编译
```bash
[root@localhost zabbix-3.2.7]# ./configure --prefix=/usr/local/zabbix --sysconfdir=/usr/local/etc/zabbix --with-mysql=/usr/local/mysql/bin/mysql_config --enable-server --enable-agent --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2
```

### 5.2.2 安装
```bash
[root@localhost zabbix-3.2.7]# make install
```

## 5.3 设置环境变量
### 5.3.1 编辑配置文件: vim /etc/profile

```bash
#zabbix env
export ZABBIX_HOME=/usr/local/zabbix/
export PATH=$PATH:$ZABBIX_HOME/bin:$ZABBIX_HOME/sbin
```

### 5.3.2 使配置立即生效
```bash
[root@gds zabbix-3.2.7]# source /etc/profile
```

# 6. LNMPZ整合

## 6.1 拷贝zabbix 页面
* 拷贝zabbix 页面所在目录php拷贝到nginx 目录中, 并重命名为zabbix 
* 修改php 目录的所有者所属组为nginx, 因为php-fpm 是以nginx:nginx 运行的

```bash
[root@gds zabbix-3.2.7]# cp -r ./frontends/php/ /var/data/nginx/php/zabbix
[root@gds zabbix-3.2.7]# chown -R nginx:nginx /var/data/nginx/php/
```

## 6.2 配置nginx
* zabbix 页面所在目录为: /var/data/nginx/php/zabbix

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
    root        /var/data/nginx/php;
}
```

## 6.3 配置PHP
* 设置php.ini: vim /usr/local/etc/php/php.ini
* 注意enable_post_data_reading = Off 一定不能打开, 否则就一直卡在首页了

```bash
post_max_size = 16M
max_execution_time = 300
max_input_time = 300
date.timezone = PRC
always_populate_raw_post_data = -1
mbstring.func_overload = 0
extension=php_gd2.dll
extension=php_gettext.dll
```

## 6.4 整合zabbix & mysql
* 重启mysql服务: service mysql restart
* 登录mysql 客户端: mysql -u root -p

### 6.4.1 创建zabbix 数据库和用户
* 创建数据库zabbix
* 导入zabbix 基础数据
* 创建zabbix 用户, 并授权

```bash
#创建数据库
mysql> CREATE DATABASE zabbix DEFAULT CHARACTER SET utf8 ;                
Query OK, 1 row affected (0.00 sec)
mysql> use zabbix;
Database changed

#导入数据
mysql> source /usr/local/src/zabbix/zabbix-3.2.7/database/mysql/schema.sql;
mysql> source /usr/local/src/zabbix/zabbix-3.2.7/database/mysql/data.sql;
mysql> source /usr/local/src/zabbix/zabbix-3.2.7/database/mysql/images.sql;

#创建用户
mysql> CREATE USER 'zabbix@%' IDENTIFIED BY 'zabbix';
Query OK, 0 rows affected (0.00 sec)

#授权zabbix访问信息
mysql> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'%' IDENTIFIED BY 'zabbix' WITH GRANT OPTION; 
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> exit
Bye
```

### 6.4.2 配置zabbix
* 修改配置文件: /usr/local/etc/zabbix/zabbix_server.conf , 修改以下配置

```bash
LogFile=/var/logs/zabbix/zabbix_server.log
LogFileSize=10
DebugLevel=3
PidFile=/var/run/zabbix/zabbix_server.pid
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
DBSocket=/usr/local/mysql/mysql.sock
DBPort=3306
Timeout=4
LogSlowQueries=3000
```

#7 启动服务

## 7.1 重启mysql
```bash
[root@gds zabbix-3.2.7]# service mysql restart
```

## 7.2 重启php
```bash
[root@gds zabbix-3.2.7]# killall php-fpm
[root@gds zabbix-3.2.7]# php-fpm
```
## 7.3 重启nginx
```bash
[root@gds zabbix-3.2.7]# nginx -s reload
```

## 7.4 重启zabbix
```bash
[root@gds zabbix-3.2.7]# killall zabbix_server
[root@gds zabbix-3.2.7]# zabbix_server
```


# 8. 初始化zabbix 配置
* 注意防火墙释放端口

## 8.1 浏览器访问
* 地址: http://192.168.1.100/zabbix/index.php, 点击next step
![](/assets/lnmpz_2017-07-28_214359.png)

## 8.2 校验php 环境
* 如果有Fail 选项, 则需修改php.ini 文件中对应的配置即可,修改后需要重启php-fpm
* 全部点ok 之后, 点next step
![](/assets/lnmpz_2017-07-28_214434.png)

## 8.3 配置数据库
* 输入mysql 数据库的相关信息, 点击next step
![](/assets/lnmpz_2017-07-28_214513.png)


## 8.4 配置zabbix_server 信息
* 配置zabbix 名称, 监听ip, 端口号, 配置好之后, 一直点next
![](/assets/lnmpz_2017-07-28_214614.png)

## 8.5 配置成功
* 注意, 到此步会生成一个zabbix_conf.php 文件, 此文件需要是nginx:nginx 权限, 如果不是的话, 需要手工设置 
![](/assets/lnmpz_2017-07-28_214710.png)

## 8.6 登录zabbix
* 默认用户名密码为: admin=zabbix
![](/assets/lnmpz_2017-07-28_214754.png)

## 8.7 修改zabbix 语言
* 默认zabbix 是全英文的, 修改为中文页面
![](/assets/lnmpz_2017-07-28_214856.png)

## 8.9 中文环境
* 到此为止, zabbix_server 就算是搭建成功了 
![](/assets/lnmpz_2017-07-28_214958.png)
























\









