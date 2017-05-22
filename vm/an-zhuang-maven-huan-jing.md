# 安装 Maven 环境
> Maven 是\

## 1. 下载
Maven 官网下载地址: [http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)
* apache-maven-3.2.5-bin.tar.gz
* apache-maven-3.5.0-bin.tar.gz

## 2. 上传
笔者将maven 安装到 /opt/app/maven 目录, maven 仓库位置: /var/data/mavenRS, maven 备份路径: /opt/source, 若目录不存在,则创建目录:

``` bash
[admin@localhost ~]$ mkdir -p /opt/app/maven/ /var/data/mavenRS/
```
目录创建完后, 使用**WinCP** 工具将maven 安装包上传到 /opt/app/maven 目录下

## 3. 解压
``` bash
[admin@localhost maven]$ tar -zxf apache-maven-3.2.5-bin.tar.gz
[admin@localhost maven]$ tar -zxf apache-maven-3.5.0-bin.tar.gz
```
## 4. 备份

## 4. 修改仓库位置

## 5. 测试