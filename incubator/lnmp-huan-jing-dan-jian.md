# LNMP 环境搭建
> 所谓的LNMP 环境指的是在Linux 系统上搭建, Nginx, Mysql, PHP 的环境. 这套环境搭建起来并不是太简单, 因为每一个环境都依赖其它一些包或软件. 笔者采用yum 方式安装依赖和mysql 环境, 源码安装nginx, php 环境.


笔者安装环境:
* Linux: centos 6.8 x64
* Nginx: 
* Mysql: 5.7
* PHP: 7.02


# 1. Nginx 环境安装
## 1. 安装前准备
### 1.1 Nginx 依赖

1. gcc: 源码包安装  
2. zlib: nginx 提供zip 模块儿, 需要zlib 支持  
3. openssl: nginx 提供ssl 模块儿, 需要openssl 支持  
4. pcre: nginx 提供地址重写rewrite功能, 需要安装perl 依赖库pcre

### 1.2 下载相关包

* [nginx-1.11.13.tar.gz](http://download.csdn.net/detail/zgf19930504/9845788)
* [ngx-fancyindex.tar.gz](http://download.csdn.net/detail/zgf19930504/9845791)

### 1.3 目录结构

* /usr/local/src/nginx
* /usr/local/src/nginx/nginx-1.11.13.tar.gz
* /usr/local/src/nginx/modules/ngx-fancyindex.tar.gz

### 1.4 解压

```bash
tar -zxvf /usr/local/src/nginx/nginx-1.11.13.tar.gz
tar -zxvf /usr/local/src/nginx/modules/ngx-fancyindex.tar.gz
```

## 2. 安装依赖环境

### 2.1 安装gcc 编译器:

```bash
yum -y gcc gcc-c++
```

### 2.2 安装zlib

```bash
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
```

## 3. 安装Nginx

### 3.1 自定义安装路径

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