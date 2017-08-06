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


## 2.2 auth 认证

## 2.3 digest 认证

## 2.4 ip 认证





