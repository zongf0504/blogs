# 安装 Maven 环境

> Maven 是一款项目管理工具, 通常用于管理项目模块儿依赖和jar包.Maven 有一仓库的概念, 下载的jar包都存储在maven 仓库中, 默认仓库位置为~/.m2, 通常笔者会手工修改maven 仓库位置. 目前maven 最新版本为 3.5.0 , 但是用3.2.5 版本的居多, 所以笔者就安装两个版本的maven. 但是,将maven 3.2.5 设置为默认maven 版本.

## 1. Maven 安装

### 1.1 下载

Maven 官网下载地址: [http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)

* apache-maven-3.2.5-bin.tar.gz
* apache-maven-3.5.0-bin.tar.gz

### 1.2 上传

笔者将maven 安装到 /opt/app/maven 目录, maven 仓库位置: /var/data/mavenRS, maven 备份路径: /opt/source, 若目录不存在,则创建目录:

```bash
[admin@localhost ~]$ mkdir -p /opt/app/maven/ /var/data/mavenRS/
```

目录创建完后, 使用**WinCP** 工具将maven 安装包上传到 /opt/app/maven 目录下

### 1.3 解压

```bash
[admin@localhost maven]$ tar -zxf apache-maven-3.2.5-bin.tar.gz
[admin@localhost maven]$ tar -zxf apache-maven-3.5.0-bin.tar.gz
```

### 1.4 备份

```bash
mv /opt/app/maven/*.tar.gz /opt/source
```

### 1.5 修改仓库位置

修改配置文件conf/settings.xml 中的settings 节点中第一行添加xml配置片段:

```xml
<localRepository>/var/data/mavenRS</localRepository>
```

### 1.6 测试

由于没有设置环境变量,所以只能使用绝对路径来执行mvn 命令.

```bash
[admin@localhost maven]$ /opt/app/maven/apache-maven-3.2.5/bin/mvn -version
Apache Maven 3.2.5 (12a6b3acb947671f09b81f49094c53f426d8cea1; 2014-12-15T01:29:23+08:00)
Maven home: /opt/app/maven/apache-maven-3.2.5
Java version: 1.7.0_80, vendor: Oracle Corporation
Java home: /opt/app/jdk/jdk1.7.0_80/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-642.el6.x86_64", arch: "amd64", family: "unix"
[admin@localhost maven]$ 
[admin@localhost maven]$ /opt/app/maven/apache-maven-3.5.0/bin/mvn -version
Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-04T03:39:06+08:00)
Maven home: /opt/app/maven/apache-maven-3.5.0
Java version: 1.7.0_80, vendor: Oracle Corporation
Java home: /opt/app/jdk/jdk1.7.0_80/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-642.el6.x86_64", arch: "amd64", family: "unix"
[admin@localhost maven]$
```

## 2. 设置环境变量

### 2.1 编辑/etc/profile 文件

```bash
[admin@localhost maven]$ vim /etc/profile
```

### 2.2 文件末尾添加配置:

```bash
# maven env  
export MAVEN_HOME=/opt/app/maven/apache-maven-3.2.5  
export PATH=$PATH:$MAVEN_HOME
```

### 2.3 使配置文件立即生效

默认修改 /etc/profile 之后并不能立即生效, 需要重新登录才能生效, 可使用**source **命令来使修改立即生效.

```bash
[admin@localhost maven]$ source /etc/profile
```

### 2.4 测试环境变量是否生效

```bash
[admin@localhost maven]$ mvn -version       
Apache Maven 3.2.5 (12a6b3acb947671f09b81f49094c53f426d8cea1; 2014-12-15T01:29:23+08:00)
Maven home: /opt/app/maven/apache-maven-3.2.5
Java version: 1.7.0_80, vendor: Oracle Corporation
Java home: /opt/app/jdk/jdk1.7.0_80/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-642.el6.x86_64", arch: "amd64", family: "unix"
[admin@localhost maven]$
```



