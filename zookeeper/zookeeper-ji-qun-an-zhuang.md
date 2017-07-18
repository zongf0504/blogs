# Zookeeper 集群安装
> 在生产环境中, zookeeper 通常都是以集群存在的, 而且通常是一台物理机上一个zookeeper. zookeeper 集群没必要有太多的机器, 通常3,5,7,9,11 台就足够了, 最常用的应该也就是3,5 台zookeeper搭建的集群了.所谓ZK 集群就是由多个ZK 单实例节点组成的一组有主从节点之分的集合.因此, 搭建集群时, 先把各个服务器上的单实例zookeeper搭建起来, 然后配置串联即可.


## 1. 集群环境
* 

| 服务器ip | 占用端口 | ZK 家目录 | ZK 数据目录 | 数据日志目录 | ZK 日志目录 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 192.168.145.100 | 2181, 2888, 3888 | /opt/app/zookeeper/zookeeper-3.4.10 | /var/data/zookeeper | /var/logs/zookeeper/datalogs| /var/logs/zookeeper/zklogs |
| 192.168.145.101 | 2181, 2888, 3888 | /opt/app/zookeeper/zookeeper-3.4.10 | /var/data/zookeeper | /var/logs/zookeeper/datalogs| /var/logs/zookeeper/zklogs |
| 192.168.145.102 | 2181, 2888, 3888 | /opt/app/zookeeper/zookeeper-3.4.10 | /var/data/zookeeper | /var/logs/zookeeper/datalogs| /var/logs/zookeeper/zklogs |


## 2. 集群环境搭建
* zk 集群有自动选主机制, 只需要将单实例串联起来就行了
* zk 集群扩展时,需要对每个zk 节点配置进行修改, 所以初期最好确定zk 集群节点数量

### 2.1 配置单实例zookeeper
* zk 单实例安装比较简单, 具体请参考笔者的: Zookeeper 单实例安装

### 2.2 串联zookeeper 集群
* zk 集群需要在每个节点配置文件zoo.cfg 中指定zk集群所有节点的地址, 因此在每台服务器节点的zoo.cfg 配置文件末尾追加:

```bash
#格式: server.序号=ip/主机名:选主通信端口号:同步数据通信端口号
server.1=192.168.145.100:2888:3888
server.2=192.168.145.101:2888:3888
server.3=192.168.145.102:2888:3888
```

### 2.3 配置myid 文件
* zk 集群需要在配置的数据目录中创建myid 文件, 文件中写入本zk示例在集群中的序号

| 服务器 | 执行命令 |
| :--- | :--- |
| 192.168.145.100 | echo 1 > /var/data/zookeeper/myid |
| 192.168.145.101 | echo 2 > /var/data/zookeeper/myid |
| 192.168.145.102 | echo 3 > /var/data/zookeeper/myid |


## 3. 集群启动
### 3.1 启动zk 集群


### 2.2 查看集群节点状态:
* 集群节点选主完全看时机, 不一定是哪个节点为leader.

192.168.145.100:
```bash
[admin@localhost zookeeper-3.4.10]$ ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: leader
```

192.168.145.101:
```bash
[root@localhost zookeeper-3.4.10]# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: follower
```

192.168.145.102:
```bash
[root@localhost zookeeper-3.4.10]# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: follower
```