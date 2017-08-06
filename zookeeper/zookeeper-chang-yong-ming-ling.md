# Zooke 常用命令
> zookeeper 提供了客户端连接工具zkCli.sh , 可以连接zookeeper 对zookeeper 进行简单的操作.

## 1. zk 客户端常用命令
* zk 客户端提供了一些命令


| 命令 | 用法 | 含义 |
| :--- | :--- | :--- |
| help | help | 查看命令帮助信息 |
| connect host:port | connect 127.0.0.1:2181 | 通过ip 端口号连接指定的Zookeeper 节点 |
| close | close | 断开当前连接的zookeeper |
| quit | quit | 退出客户端程序 |
| create [-s/e] path data acl| create -e /hw hello,world  | 创建临时节点hw |
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













