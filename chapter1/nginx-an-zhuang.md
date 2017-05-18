# Nginx 安装

> Linux 下安装nginx 并不太简单, 因为nginx 依赖一些环境.

## Nginx 依赖

1. gcc: 源码包安装  
2. zlib: nginx 提供zip 模块儿, 需要zlib 支持  
3. openssl: nginx 提供ssl 模块儿, 需要openssl 支持  
4. pcre: nginx 提供地址重写rewrite功能, 需要安装perl 依赖库pcre

## 下载相关包

安装依赖

安装gcc 编译器:

``` bash
yum -y gcc gcc-c++
```

## 安装zlib

```
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
```

## 安装Nginx

### configure

```
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

安装

```
make  && make install
```

测试

```
nginx -v   //查看版本号
nginx -V   //查看安装详情
```

Nginx 的启动管理

检测 配置文件

```
nginx -t
nginx -t -c /usr/local/nginx/nginx.conf
```

Nginx 启动

```
nginx 
nginx -c /usr/local/nginx/nginx.con
```

Nginx 停止

```
nginx -s stop
```

重启

```
nginx -s reload
```

查看帮助

```
nginx -h
```



