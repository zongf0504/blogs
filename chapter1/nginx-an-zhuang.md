# Nginx 安装

> Linux 下安装nginx 并不是特别麻烦, 只是需要安装一些依赖环境而已. Nginx 是一款优秀地web 服务器, 容易扩展,有丰富的模块儿可供选择.由于笔者会用到它的自动索引功能, 所以通常安装时,会将ngx-fancyindex 模块儿一并安装.笔者安装的是nginx-1.11.13.

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
``` bash
tar -zxvf /usr/local/src/nginx/nginx-1.11.13.tar.gz
tar -zxvf /usr/local/src/nginx/modules/ngx-fancyindex.tar.gz
```

## 2. 安装依赖环境

### 2.1 安装gcc 编译器:

``` bash
yum -y gcc gcc-c++
```

### 2.2 安装zlib

``` bash
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
```

## 3. 安装Nginx

### 3.1 自定义安装路径

``` bash
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

``` bash
make 
```

### 3.3 安装

``` bash
make install
```

### 3.4 测试

``` bash
nginx -v   //查看版本号
nginx -V   //查看安装详情
```

## 4. Nginx 的启动管理

### 4.1 检测配置文件

``` bash
nginx -t
nginx -t -c /usr/local/nginx/nginx.conf
```

### 4.2 启动服务
> nginx 默认配置监听端口为80, 但是只有root 用户才能使用80 端口, 需要修改配置文件监听端口. nginx 启动时刻指定配置文件位置, 若不指定, 使用默认配置文件.

``` bash
nginx 
nginx -c /usr/local/nginx/nginx.con
```

### 4.3 Nginx 停止
 
``` bash
nginx -s stop
```

### 4.4 重启

``` bash
nginx -s reload
```

### 4.5 查看帮助

``` bash
nginx -h
```



