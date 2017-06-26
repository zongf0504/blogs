# Kibana

> Kibana 是一个专门为ElasticSearch 提供的日志分析和监控的Web 接口.通过kibana 可对elastic 日志进行高效的搜索, 可视化分析, 可视化监控等操作.

# 1. 安装说明

仅仅安装kibana 是没有太大意义的, 需要配合elastic 才行, 所以需要先安装elastic, 而elastic 依赖于jvm 环境, 所以需要先安装jdk. 对于Elastic 和 kibana 笔者选择当前最新的5.4.2 版本, 此版本需要jdk 1.8+. 安装清单:

| 版本 | 安装位置 | 安装目录 |
| :--- | :--- | :--- |
| jdk-8u131-linux-x64.tar.gz | /opt/app/jdk | /opt/app/jdk/jdk1.8.0_131 |
| elasticsearch-5.4.2.tar.gz | /opt/app/elk/elastic/elasticsearch-5.4.2 |
| kibana-5.4.2-linux-x86_64.tar.gz | /opt/app/elk/kibana/kibana-5.4.2-linux-x86_64 |


# 2. 安装
由于jdk, elastic, kibana 提供的tar 包都是解压即可使用的, 所以直接解压就行了:

安装命令:
```bash
[admin@localhost elk]$ tar -zxf jdk-8u131-linux-x64.tar.gz -C /opt/app/jdk
[admin@localhost elk]$ tar -zxf elasticsearch-5.4.2.tar.gz -C /opt/app/elk/elastic
[admin@localhost elk]$ tar -zxf kibana-5.4.2-linux-x86_64.tar.gz -C /opt/app/elk/kibana
```

安装后:
```bash
[admin@localhost ~]$ ls ./jdk/ ./elk/elsearch/ ./elk/kibana/
./elk/elsearch/:
elasticsearch-5.4.2

./elk/kibana/:
kibana-5.4.2-linux-x86_64

./jdk/:
jdk1.8.0_131 
[admin@localhost ~]$
```

# 3. 配置

## 3.1 配置ElasticSearch

### 2.2.1 修改jdk 位置

默认启动ElasticSearch 会使用系统变量JAVA\_HOME 配置的JDK, 建议修改为指定的jdk, 即我们安装的jdk 8.   
编辑bin 目录下的elasticsearch 命令, 第一行添加:

```bash
export JAVA_HOME=/opt/app/jdk/jdk1.8.0_131
```

### 2.2.2 修改ip 端口号

ElasticSearch 的核心配置文件为 config/elasticsearch.yml, 启动之前需要修改一下ip 和端口等基本信息.  
编辑配置文件, 文件末尾追加:

```bash
bootstrap.memory_lock: false
bootstrap.system_call_filter: false       
network.host: 192.168.145.100
http.port: 9200
```

## 3.2 配置Kibana
修改kibana 的配置文件: config/kibana.yml, 文件末尾添加配置:

```bash
server.port: 5601
server.host: 172.22.12.225
elasticsearch.url: http://192.168.145.100:9200
```

# 4. 启动服务
## 4.1 启动Elastic
后台启动Elastic:
```bash
/opt/app/elk/elsearch/elasticsearch-5.4.2/bin/elasticsearch -d
```

## 4.2 启动Kibana
后台启动Kibana:
```bash
/opt/app/elk/kibana/kibana-5.4.2-linux-x86_64/bin/kibana &
```

# 5.测试
释放防火墙端口: 9200 和  5601, 浏览器输入: http:/192.168.145.100:5601
![](/assets/kibana_2017-06-26_164619.png)



