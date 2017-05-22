# 安装Apache tomcat 服务器

> Apache tomcat 是一款优秀的j轻量级Java 应用服务器, 用来运行java 项目. 在中小型系统和并发访问用户不是很多的场景下被广泛使用, 尤其是开发测试Javaweb 应用的首选服务器. 其实apache 并不是一个软件, 只是用java 语言写的一个应用而已, 所以,tomcat 的运行需要依赖jdk.

## 1. 安装tomcat
### 1.1 下载安装包

Apache 官网下载地址: [http://tomcat.apache.org/](http://tomcat.apache.org/)
笔者同时安装tomcat7 和 tomcat8
* apache-tomcat-7.0.78.tar.gz
* apache-tomcat-8.0.44.tar.gz

### 1.2 上传安装包

tomcat 安装到 /opt/app/tomcat 目录下, 备份文件存储到 /opt/source 目录下, 若目录不存在,自行创建.
使用** WinCP **工具将tomcat 压缩包上传到 /opt/app/tomcat 目录下, 使用admin 用户登录WinCP

### 1.3 解压

使用admin 用户登录linux 系统, 进入目录 /opt/app/tomcat
```bash

[admin@localhost ~]$ cd /opt/app/tomcat
[admin@localhost tomcat]$ tar -zxf apache-tomcat-7.0.78.tar.gz
[admin@localhost tomcat]$ tar -zxf apache-tomcat-8.0.44.tar.gz
[admin@localhost tomcat]$ ll
total 8
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-7-7080
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-8-8080

```

### 1.4 备份

``` bash

mv /opt/app/tomcat/*.tar.gz /opt/source

```

### 1.5 重命名
解压后的tomcat 最好按一定的规则命名, 这样便于管理.笔者通常会采用格式: ** tomcat-版本号-端口号[-应用简称]** 来命名, **端口号设定规则, 第一位代表tomcat 版本号**, 比如说: tomcat-8-8080-jenkins, 由于此时我们并不需要部署什么应用, 所以我们命名为 tomcat-7-7080, tomcat-8-8080
``` bash
[admin@localhost tomcat]$ mv tomcat-7-7080/ tomcat-7-7080/
[admin@localhost tomcat]$ mv tomcat-7-7080/ tomcat-7-7080/
[admin@localhost tomcat]$ ll
total 8
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-7-7080
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-8-8080
[admin@localhost tomcat]$ 

```

## 2. tomcat 目录讲解
* bin
* conf
* lib
* logs
* temp
* webapps
* work
* ...

## 3. tomcat 启动 & 停止

## 4. 修改tomcat 配置文件
### 1. 修改端口号
### 2. 修改get请求编码

## 5. 修改tomcat 启动文件

### 1. 修改JDK 路径
### 2. 修改JVM 内存
