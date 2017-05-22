# 安装Java 环境-jdk

> Linux 上安装jdk 是比较简单的,我们安装的是二进制包,并非源码包. 通常一台服务器上安装有多个不同版本的jdk 是正常的, 因为不同应用可能需要在不同版本上运行. 但是, 默认的jdk 只能有一个. 笔者同时安装jdk 7 和 jdk8. 
> 安装软件时,不建议再使用root 用户, 笔者使用的是admin 用户.

## 1. 下载jdk 到本地

Oracle官网下载64位Linux版的jdk7 和 jdk8, 下载地址: [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* jdk-7u80-linux-x64.tar.gz
* jdk-8u131-linux-x64.tar.gz

## 2. 上传到服务器

jdk 安装到/opt/app/jdk 目录, 压缩文件备份到 /opt/source 目录下,若目录不存在, 则通过命令创建目录:
``` bash
[admin@localhost ~]$ mkdir -p /opt/app/jdk /opt/source
```
通过 **Wincp** 软件将下载好的jdk文件,上传到 /opt/app/jdk 目录.

## 3. 解压压缩文件
切换到压缩文件所在目录, 依次解压jdk7 和 jdk8
```bash

[admin@localhost ~]$ cd /opt/app/jdk
[admin@localhost jdk]$ tar -zxf jdk-7u80-linux-x64.tar.gz
[admin@localhost jdk]$ tar -zxf jdk-8u131-linux-x64.tar.gz

```

## 5. 备份安装包
将jdk7, jdk8 的安装包备份起来, 整洁目录, 同时以备日后使用.
``` bash
mv /opt/app/jdk/*.tar.gz /opt/source
```

## 4. 测试
如果不设置环境变量的话,那么到此就已经成功安装了jdk7 和 jdk8, 只不过以后使用时都必须使用绝对路径.

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

## 6. 设置默认jdk

### 6.1 编辑/etc/profile 文件

```bash

[admin@localhost jdk]$ vim /etc/profile

```

### 6.2 文件末追加配置片段
---

\#java env  
export JAVA\_HOME=/opt/app/jdk/jdk1.7.0\_80  
export PATH=$JAVA\_HOME/bin:$PATH  
export CLASSPATH=.:$JAVA\_HOME/lib/dt.jar:$JAVA\_HOME/lib/tools.jar

---

### 6.3 设置配置文件立即生效
默认情况下,修改完/etc/profile 是不会立即生效的, 需要退出当前登录,重新登录才行.但是我们也可以使用** source **命令,令修改立即生效. 

```bash

[admin@localhost jdk]$ source /etc/profile

```

### 6.4 检测是否生效
设置成功之后, 不使用绝对路径的话, 默认使用jdk1.7; 若想使用jdk 1.8 , 需要使用绝对路径,或在启动脚本中设置全局变量JAVA_HOME的值,这个在安装tomcat 服务器时会讲到.
```bash

[admin@localhost jdk]$ java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
[admin@localhost jdk]$

```



