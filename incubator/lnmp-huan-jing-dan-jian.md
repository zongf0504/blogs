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

| 软件 | 目录 | 用途 |
| :--- | :--- | :---|
| Nginx | /usr/local/src/nginx | nginx 源码目录 |
| Nginx | /usr/local/src/nginx/modules | nginx 插件目录


# 2. Nginx 安装

## 2.1 安装依赖环境

```bash
yum -y install gcc gcc-c++ zlib zlib-devel openssl openssl-devel pcre-devel
```

### 2.1 安装Nginx
* 解压nginx压缩包, 并切换到nginx 目录: cd /usr/local/src/nginx/nginx-1.11.13

```bash
./configure \
--prefix=/var/data/nginx \
--sbin-path=/usr/sbin/nginx \
--conf-path=/etc/.nginx/nginx.conf \
--error-log-path=/var/logs/nginx/error.log \
--http-log-path=/var/logs/nginx/access.log \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/run/nginx/nginx.lock \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-stream_ssl_module \
--with-http_slice_module \
--with-http_stub_status_module \
--with-http_perl_module \
--add-module=../plugins/ngx-fancyindex/
```

### 3.2 编译

```bash
make
```

### 3.3 安装

```bash
make install
```

### 3.4 测试

```bash
nginx -v   //查看版本号
nginx -V   //查看安装详情
```








## 2. Mysql 环境安装


## 3. PHP 环境安装 