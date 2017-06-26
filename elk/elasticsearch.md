# ElasticSearch
> 

## 1. 简介


## 2. 安装
ElasticSearch 依赖于java 环境, 所以需要实现安装java 环境. 笔者安装的ElasticSearch版本是5.4.2, jdk 版本为1.8 .
* jdk-8u131-linux-x64.tar.gz
* elasticsearch-5.4.2.tar

### 2.1 安装jdk & ElasticSearch
jdk 和 elasticsearch 安装都比较简单, 直接使用 tar -zxvf xxx.tar.gz 命令解压即可, 此处就不详细描述了. 
安装路径:
* jdk: /opt/app/jdk/jdk/jdk1.8.0_131
* els: /opt/app/elk/elsearch/elasticsearch-5.4.2

### 2.2 修改配置
#### 2.2.1 修改jdk 位置
默认启动ElasticSearch 会使用系统变量JAVA_HOME 配置的JDK, 建议修改为指定的jdk, 即我们安装的jdk 8. 
编辑bin 目录下的elasticsearch 命令, 第一行添加:

```bash
export JAVA_HOME=/opt/app/jdk/jdk1.8.0_131
```