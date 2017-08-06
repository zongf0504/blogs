# Zookeeper 权限操作ACL
> 在生产环境中, zk 集群通常是部署在一组单独的服务器组上, 多个应用共同使用一个zk 集群, 以节省服务器资源. 当多个应用共同使用一个zk 集群时, 就需要对不同应用进行权限隔离了, zk 提供了简单的ACL 进行权限控制.


# 1. ACL 简介
* 传统的文件系统中, 子目录/文件默认继承父母录的ACL 权限, 但是zk 中, zNode 的ACL 权限是没有任何继承关系的, 都是独立控制的, 也就是说zk 的ACL 是针对ZNode 节点的.


## 1. zNOde 权限
* zk 中zNode 有五种权限 cdrwa, 用于控制当前节点的数据,ACL 权限修改和创建/删除子节点
* znode 中当前节点的权限不能限制对本节点的删除, 此权限由父节点控制

| 权限标识 | 含义 | 描述 |
| :--- | :--- | :--- |
| a | Admin | 允许修改本节点的ACL 权限 |
| r | Read | 允许获取本节点的数据和子节点列表 |
| w | Write | 拥有Read权限, 可修改本节点数据  |
| c | Create | 允许创建子节点 |
| d | Delete | 允许删除子节点 |


## 2. ACL 内置认证方式
zk 内置了四种ACL Schemes, 即四种认证方式, 可自行扩展, 通常使用auth 和 digest 权限控制就够了, 因为通常zk 集群都是部署在内网中, 供内网机器访问的.


| 认证方式 | 说明 | 示例 |
| :--- | :--- | :--- |
| world | 默认ACL, 只有一个唯一的id anyone, 代表所有人 | setACL /a world:anyone:cr  |
| auth | 不适用任何id, 代表任何已认证的用户| addAuth digest usr:pwd; setACL /a auth::cr |
| digest | 指定用户名密码认证 | setACL /a digest:usr:bash64(SHA1(pwd)):cr |
| ip | 客户端主机IP认证, 是客户端的主机IP做为ACL ID | setACL /a ip:192.145.1.100/0:cr |


zk 命令行中 auth 和 digest 区别:
1. auth方式, 对于一个ZNode可以同时设置多个用户访问权限, 只不过这些用户的权限是相同的: 使用addAuth 认证多个用户即可
2. digest方式, 设置权限时, 一个节点只能设置一个用户访问
3. digest 方式设置时, 密码需要使用密文, 否则不能访问, 具体密文获取方式可通过zookeeper 中自带工具实现

## 3. zk 权限相关的命令
zk ACL 相关的命令有:

| 命令 | 命令格式  | 含义  |
| :--- | :--- | :--- |
| addAuth | addAuth digest usr:pwd | 为当前登录用户添加用户认证信息 |
| create | create path value acl | 创建zNOde时同时指定ACL 权限 |
| setAcl | setAcl pathh acl | 设置zNode的ACL 权限 |
| getAcl | getAcl pathh | 获取zNode 的ACL 权限 |


# 2. 认证测试
## 2.1 world 认证
* world 认证是zk 默认的认证方式, 代表所有人,默认权限为cdrwa
* 创建zNode时, 如果不指定ACL 权限, 节点的默认权限为word:anyone:cdrwa, 即所有人拥有全部权限, 但是使用setAcl 命令时, 没有默认ACL权限, 必须制定ACL 信息

```bash
[zk: 127.0.0.1:2181(CONNECTED) 5] create /world world world:anyone:cd
Created /world
[zk: 127.0.0.1:2181(CONNECTED) 6] create /wd world              
Created /wd
[zk: 127.0.0.1:2181(CONNECTED) 7] getAcl /world
'world,'anyone
: cd
[zk: 127.0.0.1:2181(CONNECTED) 8] getAcl /wd
'world,'anyone
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 9] setAcl /wd world:anyone:cda
cZxid = 0x1c00000003
ctime = Sun Jul 30 09:07:52 CST 2017
mZxid = 0x1c00000003
mtime = Sun Jul 30 09:07:52 CST 2017
pZxid = 0x1c00000003
cversion = 0
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 5
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 10] getAcl /wd
'world,'anyone
: cda
```

## 2.2 auth 认证
* 为节点设置auth 认证模式时, 需实现使用addauth 添加认证信息, 可通过addauth添加多组认证信息, 这样两个用户对此节点拥有相同的权限
* addauth 通过用户名和密码添加认证信息, 用户密码会被采用base64(sha1(pwd))加密;

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] addauth digest mirror:mirror
[zk: 127.0.0.1:2181(CONNECTED) 1] addauth digest zong:zong
[zk: 127.0.0.1:2181(CONNECTED) 2] create /test/auth test_auth auth::cda
Created /test/auth
[zk: 127.0.0.1:2181(CONNECTED) 3] getAcl /test/auth
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cda
'digest,'zong:rJQhMsjxDyaqJWO4eOO1lOGKNxc=
: cda
[zk: 127.0.0.1:2181(CONNECTED) 4] get /test/auth
Authentication is not valid : /test/auth
[zk: 127.0.0.1:2181(CONNECTED) 7] setAcl /test/auth auth::cdrwa
cZxid = 0x1c00000008
ctime = Sun Jul 30 09:15:29 CST 2017
mZxid = 0x1c00000008
mtime = Sun Jul 30 09:15:29 CST 2017
pZxid = 0x1c00000008
cversion = 0
dataVersion = 0
aclVersion = 2
ephemeralOwner = 0x0
dataLength = 9
numChildren = 0
[zk: 127.0.0.1:2181(CONNECTED) 8] getAcl /test/auth
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cdrwa
'digest,'zong:rJQhMsjxDyaqJWO4eOO1lOGKNxc=
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 9] get /test/auth
test_auth
cZxid = 0x1c00000008
ctime = Sun Jul 30 09:15:29 CST 2017
mZxid = 0x1c00000008
mtime = Sun Jul 30 09:15:29 CST 2017
pZxid = 0x1c00000008
cversion = 0
dataVersion = 0
aclVersion = 2
ephemeralOwner = 0x0
dataLength = 9
numChildren = 0
```

## 2.3 digest 认证
* digest 认证和auth 认证非常相似, 只不过digest 认证无须实现通过addauth 添加认证用户信息, 而是直接用户名和密码设置权限, 只不过密码不是明文, 而是密文 
* digest 密文加密方式为base64(sha1(pwd)), 可以通过zk api生成, 或者在客户端使用addauth命令生成认证信息, 然后赋予一个测试节点, 然后再查看acl 信息, 这样就能看到用户名密码加密后的密文了.

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] create /test/123 1234 digest:mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=:cdrwa
Created /test/123
[zk: 127.0.0.1:2181(CONNECTED) 1] getAcl /test/123
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 2] get /test/123
Authentication is not valid : /test/123
[zk: 127.0.0.1:2181(CONNECTED) 3] addauth digest mirror:mirror
[zk: 127.0.0.1:2181(CONNECTED) 4] get /test/123
1234
cZxid = 0x1c0000000d
ctime = Sun Jul 30 09:22:45 CST 2017
mZxid = 0x1c0000000d
mtime = Sun Jul 30 09:22:45 CST 2017
pZxid = 0x1c0000000d
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 4
numChildren = 0
```

## 2.4 ip 认证
* zk ACL 支持IP 认证方式, 但这种方式笔者并不常用, 就不做过多介绍了
* ip ACL 格式:  ip:192.168.0.0/16:cdrwa


## 4. 默认根节点权限
### 1. 查看根节点ACL 权限
* 根节点默认权限为 world:anyone:cdrwa, 此权限表示任何人都能删除根节点下的所有空节点(不含子节点的权限), 因此可选择修改根目录的权限, 因此这种方式并不是太安全.

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] getAcl /
'world,'anyone
: cdrwa
```

### 2. 测试根节点默认权限
*  查看根节点权限, 创建数据

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] getAcl /
'world,'anyone
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 1] addauth digest mirror:mirror 
[zk: 127.0.0.1:2181(CONNECTED) 2] create /a a auth::cdrwa  
Created /a
[zk: 127.0.0.1:2181(CONNECTED) 3] create /a/aa a_aa auth::cdrwa
Created /a/aa
[zk: 127.0.0.1:2181(CONNECTED) 4] create /b b auth::cdrwa
Created /b
```

* 退出重新登录, 不认证用户信息, 默认为anyone; 由于a 节点存在子节点所以删除失败, 但是b 节点不存在子节点, 可以成功删除, 虽然b 拥有ACL 权限

```bash
[zk: 127.0.0.1:2181(CONNECTED) 0] getAcl /a
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 1] getAcl /a/aa
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 2] getAcl /b
'digest,'mirror:R1vnwJN21HmcYWH2CB7NwyIxB14=
: cdrwa
[zk: 127.0.0.1:2181(CONNECTED) 3] delete /b
[zk: 127.0.0.1:2181(CONNECTED) 4] rmr /a
Authentication is not valid : /a
```

### 3. 修改根节点权限
* 可以创建一管理用户, 对根节点拥有全部权限. 然后由管理员为每一个应用创建一个一级节点, 且此用户对该一级节点拥有所有权限

```
[zk: 127.0.0.1:2181(CONNECTED) 0] addauth digest admin:admin
[zk: 127.0.0.1:2181(CONNECTED) 1] setAcl / auth::cdrwa
cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x1b00000030
cversion = 20
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 0
numChildren = 2
[zk: 127.0.0.1:2181(CONNECTED) 2] getAcl /
'digest,'admin:x1nq8J5GOJVPY6zgzhtTtA9izLc=
: cdrwa
```







