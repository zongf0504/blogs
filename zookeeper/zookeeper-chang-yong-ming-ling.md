# Zooke 常用命令
> zookeeper 提供了客户端连接工具zkCli.sh , 可以连接zookeeper 对zookeeper 进行简单的操作.

# 1. zk 客户端常用命令
* zk 客户端提供了一些命令


| 命令 | 用法 | 含义 |
| :--- | :--- | :--- |
| help | help | 查看命令帮助信息 |
| connect host:port | connect 127.0.0.1:2181 | 通过ip 端口号连接指定的Zookeeper 节点 |
| close | close | 断开当前连接的zookeeper |
| quit | quit | 退出客户端程序 |
| create [-s/e] path data acl| create -e /hw hello,world  | 创建临时节点hw, -e 临时节点, -s 有序节点, 默认为持久化节点 |
| ls path | ls / | 查看子节点 |
| get path | get / | 查看节点数据  |
| set path data [version] | set /a hello | 设置节点数据 |
| delete path [version] | delete /a | 删除无子节点的节点 |
| rmr path | rmr /a | 删除节点及其子节点, 需要对节点及其子节点都拥有删除权限 |
| addauth schema auth | addauth digest mirror:mirror | 为当前连接添加认证信息 |
| setAcl path acl | setAcl /a auth::cdrwa | 为节点设置ACL权限 |
| getAcl path | getAcl /a | 获取节点的ACL 权限 |
| stat path | stat / | 查看节点状态 |
| history | history | 查看操作历史 |
| redo cmdno| redo 2 | 重新执行历史命令 |
| sync path | sync / | 向master 节点同步最新数据 |

# 2. 命令测试
## 1. zkCli.sh 连接方式
* ./zkCli.sh: 默认连接本机2181端口, 即默认连接 127.0.0.1:2181
* ./zkCli.sh -server host:port: 可直接通过-server 属性指定连接主机ip和端口号

## 2. 连接相关命令
连接相关的命令有: connect, close, quit
* connect: 客户端和zk 服务节点建立连接
* close: 断开客户端 和服务器建立的连接, 可以重新连接
* quit: 退出zk 客户端程序

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] connect 127.0.0.1:2 
[zk: 127.0.0.1:2181(CONNECTED) 0] connect 127.0.0.1:2182
2017-07-30 14:24:02,475 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@519] - EventThread shut down for session: 0x15d9208fbe40002
2017-07-30 14:24:02,476 [myid:] - INFO  [main:ZooKeeper@684] - Session: 0x15d9208fbe40002 closed
2017-07-30 14:24:02,476 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=127.0.0.1:2182 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@1cf7b8fc
2017-07-30 14:24:02,476 [myid:] - INFO  [main-SendThread(127.0.0.1:2182):ClientCnxn$SendThread@1032] - Opening socket connection to server 127.0.0.1/127.0.0.1:2182. Will not attempt to authenticate using SASL (unknown error)
2017-07-30 14:24:02,478 [myid:] - INFO  [main-SendThread(127.0.0.1:2182):ClientCnxn$SendThread@876] - Socket connection established to 127.0.0.1/127.0.0.1:2182, initiating session
2017-07-30 14:24:02,483 [myid:] - INFO  [main-SendThread(127.0.0.1:2182):ClientCnxn$SendThread@1299] - Session establishment complete on server 127.0.0.1/127.0.0.1:2182, sessionid = 0x25d9208fbe60001, negotiated timeout = 30000
[zk: 127.0.0.1:2182(CONNECTED) 1] 
WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: 127.0.0.1:2182(CONNECTED) 1] ls /
Authentication is not valid : /
[zk: 127.0.0.1:2182(CONNECTED) 2] close
2017-07-30 14:24:13,545 [myid:] - INFO  [main:ZooKeeper@684] - Session: 0x25d9208fbe60001 closed
[zk: 127.0.0.1:2182(CLOSED) 3] 2017-07-30 14:24:13,546 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@519] - EventThread shut down for session: 0x25d9208fbe60001

[zk: 127.0.0.1:2182(CLOSED) 3] quit
Quitting...
```


## 3. 节点操作命令
* 对于zNode 节点数据的增删改查相关的节点有: create, ls, get, set, delete, rmr, stat
* zNode 节点保存的数据信息:

| 字段 | 含义 |
| :--- | :--- |
| czxid | 节点被创建时的事务id |
| ctime | 节点上次修改的事务id |
| mzxid | 节点上次修改的事务id |
| mtime | 节点上次修改的时间 |
| dataversion | 节点被修改的版本号 |
| datalengh | 节点存储的数据的长度, 单位字节 |
| aclversion | 节点的ACL 被修改的版本号 |
| ephemeralOwner | 临时节点的拥有者的sessionid, 持久性节点为0 |
| numChildren | 子节点的数量 |
| cversion | 子节点变化版本号, 新增/删除子节点时会自增 |
| pzxid | 子节点点最近修改\(新增/删除\)的zxid |

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] create /zNode zNode
Created /zNode
[zk: 127.0.0.1:2181(CONNECTED) 1] create /zNode/A aa
Created /zNode/A
[zk: 127.0.0.1:2181(CONNECTED) 2] create /zNode/B bb
Created /zNode/B
[zk: 127.0.0.1:2181(CONNECTED) 3] ls /zNode
[A, B]
[zk: 127.0.0.1:2181(CONNECTED) 4] get /zNode/A
aa
cZxid = 0x1d00000035
ctime = Sun Jul 30 14:30:18 CST 2017
mZxid = 0x1d00000035
mtime = Sun Jul 30 14:30:18 CST 2017
pZxid = 0x1d00000035
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 2
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 5] set /zNode/A abcdefg
cZxid = 0x1d00000035
ctime = Sun Jul 30 14:30:18 CST 2017
mZxid = 0x1d00000037
mtime = Sun Jul 30 14:30:55 CST 2017
pZxid = 0x1d00000035
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 7
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 6] get /zNode/A
abcdefg
cZxid = 0x1d00000035
ctime = Sun Jul 30 14:30:18 CST 2017
mZxid = 0x1d00000037
mtime = Sun Jul 30 14:30:55 CST 2017
pZxid = 0x1d00000035
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 7
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 7] stat /zNode
cZxid = 0x1d00000034
ctime = Sun Jul 30 14:30:12 CST 2017
mZxid = 0x1d00000034
mtime = Sun Jul 30 14:30:12 CST 2017
pZxid = 0x1d00000036
cversion = 2
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 5
numChildren = 2
[zk: 127.0.0.1:2181(CONNECTED) 8] ls /zNode
[A, B]
[zk: 127.0.0.1:2181(CONNECTED) 9] delete /zNode/A
[zk: 127.0.0.1:2181(CONNECTED) 10] ls /zNode
[B]
[zk: 127.0.0.1:2181(CONNECTED) 11] delete /zNode
Node not empty: /zNode
[zk: 127.0.0.1:2181(CONNECTED) 12] rmr /zNode
[zk: 127.0.0.1:2181(CONNECTED) 13] ls /zNode
Node does not exist: /zNode
```

## 4. 权限相关命令
* zk 使用ACL 对节点进行权限控制, 与权限控制相关的命令有: create , setAcl, getAcl
* 测试ACL 权限时, 使用addAuth 认证权限之后, 设置ACL 权限节点后, 需要退出当前连接测试

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] addauth digest mirror:mirror
[zk: 127.0.0.1:2181(CONNECTED) 1] create /authNode anode auth::cdrwa
Created /authNode
[zk: 127.0.0.1:2181(CONNECTED) 2] getAcl /authNode
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 3] getAcl /
'world,'anyone
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 4] get /authNode
anode
cZxid = 0x1d0000003e
ctime = Sun Jul 30 14:33:04 CST 2017
mZxid = 0x1d0000003e
mtime = Sun Jul 30 14:33:04 CST 2017
pZxid = 0x1d0000003e
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 5
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 5] close
2017-07-30 14:33:45,589 [myid:] - INFO  [main:ZooKeeper@684] - Session: 0x15d9208fbe4000b closed
[zk: 127.0.0.1:2181(CLOSED) 6] 2017-07-30 14:33:45,590 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@519] - EventThread shut down for session: 0x15d9208fbe4000b

[zk: 127.0.0.1:2181(CLOSED) 6] connect 127.0.0.1:2181
2017-07-30 14:33:56,136 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=127.0.0.1:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@46938420
2017-07-30 14:33:56,137 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1032] - Opening socket connection to server 127.0.0.1/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2017-07-30 14:33:56,139 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@876] - Socket connection established to 127.0.0.1/127.0.0.1:2181, initiating session
[zk: 127.0.0.1:2181(CONNECTING) 7] 2017-07-30 14:33:56,146 [myid:] - INFO  [main-SendThread(127.0.0.1:2181):ClientCnxn$SendThread@1299] - Session establishment complete on server 127.0.0.1/127.0.0.1:2181, sessionid = 0x15d9208fbe4000c, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: 127.0.0.1:2181(CONNECTED) 7] get /authNode
Authentication is not valid : /authNode
[zk: 127.0.0.1:2181(CONNECTED) 8] addauth digest mirror:mirror
[zk: 127.0.0.1:2181(CONNECTED) 9] get /authNode
anode
cZxid = 0x1d0000003e
ctime = Sun Jul 30 14:33:04 CST 2017
mZxid = 0x1d0000003e
mtime = Sun Jul 30 14:33:04 CST 2017
pZxid = 0x1d0000003e
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 5
numChildren = 0
```


## 5. 操作历史命令
* 与操作历史相关的命令有: history, redo```bash
[zk: 127.0.0.1:2181(CONNECTED) 1] ls /
[wd, authNode, test, world, zookeeper, a]
[zk: 127.0.0.1:2181(CONNECTED) 2] ls /authNode
Authentication is not valid : /authNode
[zk: 127.0.0.1:2181(CONNECTED) 3] ls /zookeeper
[quota]
[zk: 127.0.0.1:2181(CONNECTED) 4] history
0 - history
1 - ls /
2 - ls /authNode
3 - ls /zookeeper
4 - history
[zk: 127.0.0.1:2181(CONNECTED) 5] redo 1
[wd, authNode, test, world, zookeeper, a]
```

## 6. 其它命令
* zk 还有其它的一些命令, 如: redo, sync, setquoota, delquota 等

```bash
[zk: 127.0.0.1:2181(CONNECTED) 7] help
ZooKeeper -server host:port cmd args
        connect host:port
        get path [watch]
        ls path [watch]
        set path data [version]
        rmr path
        delquota [-n|-b] path
        quit 
        printwatches on|off
        create [-s] [-e] path data acl
        stat path [watch]
        close 
        ls2 path [watch]
        history 
        listquota path
        setAcl path acl
        getAcl path
        sync path
        redo cmdno
        addauth scheme auth
        delete path [version]
        setquota -n|-b val path
```










