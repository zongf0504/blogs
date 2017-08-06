# Zookeeper 集群安装

> 在生产环境中, zookeeper 通常都是以集群存在的, 而且通常是一台物理机上一个zookeeper. zookeeper 集群没必要有太多的机器, 通常3,5,7,9,11 台就足够了, 最常用的应该也就是3,5 台zookeeper搭建的集群了.所谓ZK 集群就是由多个ZK 单实例节点组成的一组有主从节点之分的集合.因此, 搭建集群时, 先把各个服务器上的单实例zookeeper搭建起来, 然后配置串联即可.

## 1. 集群环境

| 服务器ip | 占用端口 | ZK 家目录 | ZK 数据目录 | 数据快照目录 | ZK 日志目录 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 192.168.145.100 | 2181, 2888, 3888 | /opt/app/zookeeper/zookeeper-3.4.10 | /var/data/zookeeper | /var/data/zookeeper/datalogs | /var/logs/zookeeper/zklogs |
| 192.168.145.101 | 2181, 2888, 3888 | /opt/app/zookeeper/zookeeper-3.4.10 | /var/data/zookeeper | /var/data/zookeeper/datalogs | /var/logs/zookeeper/zklogs |
| 192.168.145.102 | 2181, 2888, 3888 | /opt/app/zookeeper/zookeeper-3.4.10 | /var/data/zookeeper | /var/data/zookeeper/datalogs | /var/logs/zookeeper/zklogs |

## 2. 集群环境搭建

* zk 集群有自动选主机制, 只需要将单实例串联起来就行了
* zk 集群扩展时,需要对每个zk 节点配置进行修改, 所以初期最好确定zk 集群节点数量

### 2.1 配置单实例zookeeper

* zk 单实例安装比较简单, 具体请参考笔者的: Zookeeper 单实例安装

### 2.2 串联zookeeper 集群

* zk 集群需要在每个节点配置文件zoo.cfg 中指定zk集群所有节点的地址, 因此在每台服务器节点的zoo.cfg 配置文件末尾追加:

```bash
#格式: server.序号=ip/主机名:同步数据通信端口号:选主通信端口号
server.1=192.168.145.100:2888:3888
server.2=192.168.145.101:2888:3888
server.3=192.168.145.102:2888:3888
```

### 2.3 配置myid 文件

* zk 集群需要在配置的数据目录中创建myid 文件, 文件中写入本zk示例在集群中的序号

| 服务器 | 执行命令 |
| :--- | :--- |
| 192.168.145.100 | echo 1 &gt; /var/data/zookeeper/myid |
| 192.168.145.101 | echo 2 &gt; /var/data/zookeeper/myid |
| 192.168.145.102 | echo 3 &gt; /var/data/zookeeper/myid |

## 3. 集群启动

* 依次启动集群节点: ./bin/zkServer.sh start
* 集群节点选主完全看时机, 不一定是哪个节点为leader. 节点半数启动成功之后, 集群才能启动成功
* 集群创建成功的标志是为每个节点分配了角色: leader, follwer, observer

192.168.145.100: leader

```bash
[admin@localhost zookeeper-3.4.10]$ ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: leader
```

192.168.145.101: follwer

```bash
[root@localhost zookeeper-3.4.10]# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: follower
```

192.168.145.102: follwer

```bash
[root@localhost zookeeper-3.4.10]# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: follower
```

## 附:

* 集群半数节点启动成功后, 集群才能连接成功.否则只是节点启动成功, 并没有成功创建集群
* 成功创建集群的标志是,为每个节点分配了角色: leader, follwer, observer
* 注意释放各个节点的2181, 2888, 3888 端口, 保证节点之间能正常通信

```bash
[admin@localhost zookeeper-3.4.10]$ ./bin/zkServer.sh status              
ZooKeeper JMX enabled by default
Using config: /home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
[admin@localhost zookeeper-3.4.10]$ jps -l | grep zookeeper               
11739 org.apache.zookeeper.server.quorum.QuorumPeerMain
[admin@localhost zookeeper-3.4.10]$ ps -ef | grep zookeeper | grep -v grep
zongf    11739     1  0 11:12 pts/3    00:00:00 /opt/app/jdk1.6.0_31/bin/java -Dzookeeper.log.dir=. -Dzookeeper.root.logger=INFO,CONSOLE -cp /home/zongf/zookeeper/zookeeper-3.4.10/bin/../build/classes:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../build/lib/*.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../lib/slf4j-log4j12-1.6.1.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../lib/slf4j-api-1.6.1.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../lib/netty-3.10.5.Final.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../lib/log4j-1.2.16.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../lib/jline-0.9.94.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../zookeeper-3.4.10.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../src/java/lib/*.jar:/home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf: -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false org.apache.zookeeper.server.quorum.QuorumPeerMain /home/zongf/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
```



