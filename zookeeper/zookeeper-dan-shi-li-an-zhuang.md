# Zookeeper 单实例安装

> Zookeeper 通常都是采用集群方式安装, 所谓集群就是由一个一个的单实例zookeeper单实例串联起来的, 因此单实例安装是非常必要的.而且在学习时, 一个zookeeper实例就够用了. Zookeeper 依赖于Java 环境.

## 1. 安装准备

* 安装java 环境
* 下载zookeeper包: zookeeper-3.4.10.tar.gz

## 2. 安装

zookeeper 安装是比较简单的, 我们对安装目录做一下简单的规划, 这些目录需要手动创建

| 目录 | 用途 |
| :--- | :--- |
| /opt/app/zookeeper/zookeeper-3.4.10 | zookeeper 安装家目录 |
| /var/data/zookeeper | zookeeper 数据存放目录 |
| /var/logs/zookeeper/zklogs | zookeeper 数据存放目录 |
| /var/logs/zookeeper/datalogs | zookeeper 数据日志 |

| 配置/脚本 | 用途 |
| :--- | :--- |
| conf/zoo.cfg | zookeeper 的核心配置文件 |
| conf/log4j.properties | zookeeper 的日志输出格式配置文件, zookeeper是用java 写的, 用log4j 输出日志 |
| bin/zkEnv.sh | zookeeper 的环境配置脚本 |
| bin/zkServer.sh | zookeeper 的启动管理脚本 |
| bin/zkCli.sh | zookeeper的客户端连接脚本


### 2.1 安装zookeeper

* zookeeper 安装是非常简单的, 和tomcat类似, 直接解压即可.

```bash
[admin@localhost zookeeper]$ tar -zxf zookeeper-3.4.10.tar.gz
```

### 2.2 创建配置文件zoo.cfg

* zookeeper 默认使用配置文件 conf/zoo.cfg , 但是conf目录下默认是没有此目录的, 因此需要新建.直接复制conf 目录下的zoo\_aample.cfg 文件就行

```bash
[admin@localhost zookeeper] cd /opt/app/zookeeper/zookeeper-3.4.10/conf
[admin@localhost zookeeper-3.4.10]$ cp zoo_sample.cfg zoo.cfg
```

### 2.3 编辑配置文件内容

```bash
#心跳时间
tickTime=2000
initLimit=10
syncLimit=5
#数据存放目录
dataDir=/var/data/zookeeper
#数据日志存放目录
dataLogDir=/var/logs/datalogs
#客户端连接端口
clientPort=2181
```

### 2.3 修改启动日志文件

* zookeeper 启动时,默认会在你执行命令的当前目录创建一个zookeeper.out 日志文件, 而且zoo.cfg 的配置文件中并不能修改zookeeper启动日志文件位置, 只能通过修改 bin/zkEnv.sh 脚本中的设置
* 将 bin/zkEnv.sh 中的 ZOO\_LOG\_DIR 修改为自定义日志目录: /var/logs/zookeeper/zklogs 

```bash
if [ "x${ZOO_LOG_DIR}" = "x" ]
then
    ZOO_LOG_DIR="/var/logs/zookeeper/zklogs"
fi
```

### 2.4 修改log4j 配置

* zookeeper 默认使用log4j 打印日志, log4j 的配置文件在conf 目录下, 可以按需修改log4j 的日志打印信息

## 3. zookeeper 启动管理

zookeeper 的相关命令都在bin 目录下, 启动停止是通过zkServer.sh 来完成

* zkServer.sh: zookeeper 启动,停止,查看状态脚本
* zkCli.sh : zookeeper 客户端连接工具
* zkEnv.sh : zookeeper 环境设置脚本
* zkCleanup.sh : 清理老的事务和快照脚本

### 3.1 启动zookeeper

* 命令: ./zkServer.sh start 

```bash
[admin@localhost bin]$ ./zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

### 3.2 查看zookeeper状态

* 命令: ./zkServer.sh status

```bash
[admin@localhost bin]$ ./zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Mode: standalone
```

### 3.3 查看zookeeper 进程

* 通过ps 查看进程: psgrep zookeeper
* 通过jps 查看进程: jps -l \| grep zookeeper

```bash
[admin@gds bin]$ psgrep zookeeper
[1] admin     8845     1  1 10:14 pts/2    00:00:00 /opt/app/jdk/jdk1.6.0_31/bin/java -Dzookeeper.log.dir=/var/logs/zookeeper/zklogs -Dzookeeper.root.logger=INFO,CONSOLE -cp /home/admin/zookeeper/zookeeper-3.4.10/bin/../build/classes:/home/admin/zookeeper/zookeeper-3.4.10/bin/../build/lib/*.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../lib/slf4j-log4j12-1.6.1.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../lib/slf4j-api-1.6.1.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../lib/netty-3.10.5.Final.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../lib/log4j-1.2.16.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../lib/jline-0.9.94.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../zookeeper-3.4.10.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../src/java/lib/*.jar:/home/admin/zookeeper/zookeeper-3.4.10/bin/../conf:/opt/app/jdk/jdk1.6.0_31/lib -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false org.apache.zookeeper.server.quorum.QuorumPeerMain /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
[admin@gds bin]$ jps -l | grep zookeeper
8845 org.apache.zookeeper.server.quorum.QuorumPeerMain
```

### 3.4 重启zookeeper

* 命令: ./zkServer.sh restart

```bash
[admin@localhost bin]$ ./zkServer.sh restart
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

### 3.5 停止zookeeper

* 命令: ./zkServer.sh stop

```bash
[admin@localhost bin]$ ./zkServer.sh stop
ZooKeeper JMX enabled by default
Using config: /home/admin/zookeeper/zookeeper-3.4.10/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
```



