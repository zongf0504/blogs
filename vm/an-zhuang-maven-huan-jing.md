# 安装 Maven 环境
> Maven 是一款项目管理工具, 通常用于管理项目模块儿依赖和jar包.Maven 有一仓库的概念, 下载的jar包都存储在maven 仓库中, 默认仓库位置为~/.m2, 通常笔者会手工修改maven 仓库位置.

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
``` bash
mv /opt/app/maven/*.tar.gz /opt/source
```

## 5. 修改仓库位置

ma修改配置文件conf/settings.xml 中的settings 节点中第一行添加xml配置片段: 
``` xml
<localRepository>/var/data/mavenRS</localRepository>
```

## 6. 测试