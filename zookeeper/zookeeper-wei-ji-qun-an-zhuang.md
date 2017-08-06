# Zookeeper 伪集群安装

> 所谓伪集群, 也就是说在一台物理机上部署多个zk节点组成zk 集群. 这种方式并不是太常用, 也就用于平时的学习中

## 1. 集群环境

| 服务器ip | 占用端口 | ZK 家目录 | ZK 数据目录 | 数据快照目录 | ZK 日志目录 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 192.168.145.100 | 2181, 2878, 3878 | /opt/app/zookeeper/2181/zookeeper-3.4.10-2181 | /var/data/zookeeper/2181 | /var/data/zookeeper/2181/datalogs | /var/logs/zookeeper/2181/zklogs |
| 192.168.145.100 | 2182, 2888, 3888 | /opt/app/zookeeper/2182/zookeeper-3.4.10-2182 | /var/data/zookeeper/2182 | /var/data/zookeeper/2182/datalogs | /var/logs/zookeeper/2182/zklogs |
| 192.168.145.100 | 2183, 2898, 3898 | /opt/app/zookeeper/2183/zookeeper-3.4.10-2182 | /var/data/zookeeper/2183 | /var/data/zookeeper/2183/datalogs | /var/logs/zookeeper/2183/zklogs |

## 2. 集群环境搭建

* zk 集群有自动选主机制, 只需要将单实例串联起来就行了
* zk 集群扩展时,需要对每个zk 节点配置进行修改, 所以初期最好确定zk 集群节点数量

### 2.1 配置单实例zookeeper

* zk 单实例安装比较简单, 具体请参考笔者的: Zookeeper 单实例安装

### 2.2 串联zookeeper 集群

* zk 集群需要在每个节点配置文件zoo.cfg 中指定zk集群所有节点的地址, 因此在每台服务器节点的zoo.cfg 配置文件末尾追加:

```bash
#格式: server.序号=ip/主机名:同步数据通信端口号:选主通信端口号
server.1=192.168.145.100:2878:3878
server.2=192.168.145.100:2888:3888
server.3=192.168.145.100:2898:3898
```

### 2.3 配置myid 文件

* zk 集群需要在配置的数据目录中创建myid 文件, 文件中写入本zk示例在集群中的序号

| 服务器 | 执行命令 |
| :--- | :--- |
| 192.168.145.100 | echo 1 &gt; /var/data/zookeeper/2181/myid |
| 192.168.145.100 | echo 2 &gt; /var/data/zookeeper/2182/myid |
| 192.168.145.100 | echo 3 &gt; /var/data/zookeeper/2183/myid |

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

## 4. 集群管理脚本
### 4.1 集群管理脚本
* 脚本名称: zk-cluster.sh
* 用于启动集群, 停止集群, 查看集群节点状态

```bash
#!/bin/bash
#Desc 本机zk 伪集群管理
#Auth zongf

if [ "statusx" == $1x -o "startx" == $1x -o "stopx" == $1x -o "restartx" == $1x ] ; then
    echo -e "\n $1 2181:"
    /opt/app/zookeeper/2181/zookeeper-3.4.10/bin/zkServer.sh $1
    
    echo -e "\n $1 2182:"
    /opt/app/zookeeper/2182/zookeeper-3.4.10/bin/zkServer.sh $1
    
    echo -e "\n $1 2183:"
    /opt/app/zookeeper/2183/zookeeper-3.4.10/bin/zkServer.sh $1
    
elif [ "jpsx" == $1x ] ; then
    jps -m | grep QuorumPeerMain | grep zookeeper
else
    echo "Command demo: $0 start|stop|restart|status|jps"
fi
```

### 4.2 zk 服务管理脚本
* 脚本名称: zk-server.sh
* 管理zk 单个节点启动,停止,重启

```bash
#!/bin/bash
#Desc 本机zk 节点管理
#Auth zongf

if [ "1x" == $1x -o "2x" == $1x -o "3x" == $1x ] ; then
   if [ "startx" == $2x -o "stopx" == $2x -o "restartx" == $2x -o "statusx" == $2x ] ; then 
      echo -e "zookeeper 218$1 $2:"
      /opt/app/zookeeper/218$1/zookeeper-3.4.10/bin/zkServer.sh $2 
   else
      echo "Command demo: $0 1|2|3 start|stop|restart|status"
   fi
else 
   echo "Command demo: $0 1|2|3 start|stop|restart|status"
fi
```


### 4.3 zk 客户端连接脚本
* 脚本名称: zk-cli.sh
* zk 客户端连接集群节点脚本

```bash
#!/bin/bash
#Desc zk客户端连接
#Auth zongf


if [ "1x" == $1x -o "2x" == $1x -o "3x" == $1x ] ; then
    echo -e "connect zookeeper $1:"
    /opt/app/zookeeper/218$1/zookeeper-3.4.10/bin/zkCli.sh -server 127.0.0.1:218$1
else
    echo "Command demo: $0 1|2|3"
fi

```

### 4.4 脚本测试
```bash
[admin@localhost bin]$ ./zk-cluster.sh start

 start 2181:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

 start 2182:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2182/zookeeper-3.4.10/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

 start 2183:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2183/zookeeper-3.4.10/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[admin@localhost bin]$ ./zk-cluster.sh status

 status 2181:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: follower

 status 2182:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2182/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: follower

 status 2183:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2183/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: leader
[admin@localhost bin]$ ./zk-cluster.sh jps
1925 QuorumPeerMain /opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../conf/zoo.cfg
1941 QuorumPeerMain /opt/app/zookeeper/2182/zookeeper-3.4.10/bin/../conf/zoo.cfg
1965 QuorumPeerMain /opt/app/zookeeper/2183/zookeeper-3.4.10/bin/../conf/zoo.cfg
[admin@localhost bin]$ ./zk-server.sh 2 stop
zookeeper 2182 stop:
ZooKeeper JMX enabled by default
Using config: /opt/app/zookeeper/2182/zookeeper-3.4.10/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
[admin@localhost bin]$ ./zk-cli.sh 1
connect zookeeper 1:
Connecting to 127.0.0.1:2181
2017-07-29 18:09:15,154 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.10-39d3a4f269333c922ed3db283be479f9deacaa0f, built on 03/23/2017 10:13 GMT
2017-07-29 18:09:15,159 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=localhost
2017-07-29 18:09:15,159 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.7.0_80
2017-07-29 18:09:15,161 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2017-07-29 18:09:15,161 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/opt/app/jdk/jdk1.7.0_80/jre
2017-07-29 18:09:15,161 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../build/classes:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../build/lib/*.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../lib/slf4j-log4j12-1.6.1.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../lib/slf4j-api-1.6.1.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../lib/netty-3.10.5.Final.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../lib/log4j-1.2.16.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../lib/jline-0.9.94.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../zookeeper-3.4.10.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../src/java/lib/*.jar:/opt/app/zookeeper/2181/zookeeper-3.4.10/bin/../conf:.:/opt/app/jdk/jdk1.7.0_80/lib/dt.jar:/opt/app/jdk/jdk1.7.0_80/lib/tools.jar
2017-07-29 18:09:15,161 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=2.6.32-642.el6.x86_64
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=admin
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/home/admin
2017-07-29 18:09:15,162 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/opt/app/zookeeper/bin
2017-07-29 18:09:15,164 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=127.0.0.1:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@2f49f848
2017-07-29 18:09:15,199 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1032] - Opening socket connection to server 127.0.0.1/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
Welcome to ZooKeeper!
2017-07-29 18:09:15,223 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@876] - Socket connection established to 127.0.0.1/127.0.0.1:2181, initiating session
JLine support is enabled
[zk: 127.0.0.1:2181(CONNECTING) 0] 2017-07-29 18:09:15,310 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1299] - Session establishment complete on server 127.0.0.1/127.0.0.1:2181, sessionid = 0x15d8dd1d5c50000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: 127.0.0.1:2181(CONNECTED) 0] ls
[zk: 127.0.0.1:2181(CONNECTED) 1] ls /
[b, zookeeper, a]
[zk: 127.0.0.1:2181(CONNECTED) 2] quit
Quitting...
2017-07-29 18:09:21,700 [myid:] - INFO  [main:ZooKeeper@684] - Session: 0x15d8dd1d5c50000 closed
2017-07-29 18:09:21,702 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@519] - EventThread shut down for session: 0x15d8dd1d5c50000

```



