# Zabbix 环境搭建
> Zabbix 是一个分布式服务器监控系统, 安装依赖于LNMP 或 LAMP 环境, 笔者选择的是LNMP. LNMP的搭建不在本篇讨论范围之内.

## 1.1 环境依赖:
* Linux: centos 6.8 x64
* Nginx: nginx-1.11.13.tar.gz
* Mysql: mysql57-community-release-el6-9.noarch.rpm
* PHP: php-7.1.7.tar.bz2
* Zabbix: zabbix-3.0.10.tar.gz

## 2. 安装Zabbix

### 2.1
--prefix=/var/data/nginx \
