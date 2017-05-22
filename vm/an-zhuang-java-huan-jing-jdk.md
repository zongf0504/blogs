# 安装Java 环境-jdk
> Linux 上安装jdk 是比较简单的,我们安装的是二进制包,并非源码包. 通常一台服务器上安装有多个不同版本的jdk 是正常的, 因为不同应用可能需要在不同版本上运行. 但是, 默认的jdk 只能有一个. 笔者同时安装jdk 7 和 jdk8. 

## 1. 下载:

官网下载linux 下的64 位的jdk7 和 jdk8, 下载地址: [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* jdk-7u80-linux-x64.tar.gz
* jdk-8u131-linux-x64.tar.gz

## 2. 上传到服务器
通过wincp 上传到服务器的 /opt/app/jdk 目录下

## 3. 解压

``` bash
[admin@localhost ~]$ cd /opt/app/jdk
[admin@localhost jdk]$ tar -zxf jdk-7u80-linux-x64.tar.gz
[admin@localhost jdk]$ tar -zxf jdk-8u131-linux-x64.tar.gz 
```

## 4. 测试

```
[admin@localhost jdk]$ /opt/app/jdk/jdk1.7.0_80/bin/java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
[admin@localhost jdk]$ /opt/app/jdk/jdk1.8.0_131/bin/java -version/opt/app/jdk/jdk1.8.0_131/bin/java -version
Unrecognized option: -version/opt/app/jdk/jdk1.8.0_131/bin/java
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
[admin@localhost jdk]$ 
[admin@localhost jdk]$ /opt/app/jdk/jdk1.8.0_131/bin/java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
[admin@localhost jdk]$ 
```

## 5. 设置环境变量

``` bash
[admin@localhost jdk]$ vim /etc/profile
```
***
\#java env
export JAVA_HOME=/opt/app/jdk/jdk1.7.0_80
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
***

## 6. 设置配置文件立即生效
``` bash
[admin@localhost jdk]$ source /etc/profile
``` 

## 7. 检测是否生效

``` bash
[admin@localhost jdk]$ java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
[admin@localhost jdk]$ 
```
