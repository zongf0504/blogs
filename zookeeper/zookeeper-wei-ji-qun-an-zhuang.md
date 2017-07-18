# Zookeeper 伪集群安装

> 所谓伪集群, 也就是说在一台物理机上部署多个zk节点组成zk 集群. 这种方式并不是太常用, 也就用于平时的学习中

## 1. 集群环境

| 服务器ip | 占用端口 | ZK 家目录 | ZK 数据目录 | 数据日志目录 | ZK 日志目录 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 192.168.145.100 | 2181, 2878, 3878 | /opt/app/zookeeper/2181/zookeeper-3.4.10-2181 | /var/data/zookeeper/2181 | /var/logs/zookeeper/2181/datalogs | /var/logs/zookeeper/2181/zklogs |
| 192.168.145.100 | 2182, 2888, 3888 | /opt/app/zookeeper/2182/zookeeper-3.4.10-2182 | /var/data/zookeeper/2182 | /var/logs/zookeeper/2182/datalogs | /var/logs/zookeeper/2182/zklogs |
| 192.168.145.100 | 2183, 2898, 3898 | /opt/app/zookeeper/2183/zookeeper-3.4.10-2182 | /var/data/zookeeper/2183 | /var/logs/zookeeper/2183/datalogs | /var/logs/zookeeper/2183/zklogs |

## 2. 集群环境搭建

* zk 集群有自动选主机制, 只需要将单实例串联起来就行了
* zk 集群扩展时,需要对每个zk 节点配置进行修改, 所以初期最好确定zk 集群节点数量

### 2.1 配置单实例zookeeper

* zk 单实例安装比较简单, 具体请参考笔者的: Zookeeper 单实例安装

### 2.2 串联zookeeper 集群

* zk 集群需要在每个节点配置文件zoo.cfg 中指定zk集群所有节点的地址, 因此在每台服务器节点的zoo.cfg 配置文件末尾追加:

```bash
#格式: server.序号=ip/主机名:选主通信端口号:同步数据通信端口号
server.1=192.168.145.100:2878:3878
server.2=192.168.145.100:2888:3888
server.3=192.168.145.100:2898:3898
```

### 2.3 配置myid 文件

* zk 集群需要在配置的数据目录中创建myid 文件, 文件中写入本zk示例在集群中的序号

| 服务器 | 执行命令 |
| :--- | :--- |
| 192.168.145.100 | echo 1 &gt; /var/data/zookeeper/2181/myid |
| 192.168.145.101 | echo 2 &gt; /var/data/zookeeper/2182/myid |
| 192.168.145.102 | echo 3 &gt; /var/data/zookeeper/2183/myid |

## 3. 集群启动

* 依次启动集群节点: ./bin/zkServer.sh start
* 集群节点选主完全看时机, 不一定是哪个节点为leader. 节点半数启动成功之后, 集群才能启动成功
* 集群创建成功的标志是为每个节点分配了角色: leader, follwer, observer

### 3.1 启动集群
```bash
[admin@localhost zookeeper]$ ./zookeeper-3.4.10-2181/bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10-2181/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[admin@localhost zookeeper]$ ./zookeeper-3.4.10-2182/bin/zkServer.sh start 
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10-2182/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[admin@localhost zookeeper]$ ./zookeeper-3.4.10-2183/bin/zkServer.sh start 
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10-2183/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

### 3.2 查看集群状态
* 每个节点成功分配角色则表示集群启动成功.

```bash
[admin@localhost zookeeper]$ ./zookeeper-3.4.10-2181/bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10-2181/bin/../conf/zoo.cfg
Mode: follower
[admin@localhost zookeeper]$ ./zookeeper-3.4.10-2182/bin/zkServer.sh status 
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10-2182/bin/../conf/zoo.cfg
Mode: leader
[admin@localhost zookeeper]$ ./zookeeper-3.4.10-2183/bin/zkServer.sh status 
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10-2183/bin/../conf/zoo.cfg
Mode: follower
```



