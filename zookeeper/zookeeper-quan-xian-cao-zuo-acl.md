# Zookeeper 权限操作ACL
> 在生产环境中, zk 集群通常是部署在一组单独的服务器组上, 多个应用共同使用一个zk 集群, 以节省服务器资源. 当多个应用共同使用一个zk 集群时, 就需要对不同应用进行权限隔离了, zk 提供了简单的ACL 进行权限控制.


# ACL
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
* zk 内置了四种ACL Schemes, 即四种认证方式, 可自行扩展
* 通常常用的权限控制是auth 和 digest


| 认证方式 | 说明 | 示例 |
| :--- | :--- | :--- |
| world | 默认ACL, 只有一个唯一的id anyone, 代表所有人 | setACL /a world:anyone:cr  |
| auth | 不适用任何id, 代表任何已认证的用户| addAuth digest zong:zong; setACL /a auth::cr |
| digest | 指定用户名密码认证 | setACL /a digest:zong:zong:cr |
| ip | 客户端主机IP认证, 是客户端的主机IP做为ACL ID | setACL /a ip:192.145.1.100/0:cr |