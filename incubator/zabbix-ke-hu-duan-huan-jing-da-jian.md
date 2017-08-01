# Zabbix 客户端安装
> Zabbix 除了安装服务器端, 还需要安装客户端, 客户端安装是比较简单. 


# Zabbix Agentd 安装
* zabbix agentd 版本: 

## 1. 安装准备工作

### 1.1 下载安装包 
* 笔者使用源码安装, 安装版本为: zabbix-3.2.7.tar.gz

### 1.2 创建用户
* zabbix 需要使用zabbix 用户启动, 因此创建zabbix 用户

```bash
[root@localhost zabbix]# useradd -s /sbin/nologin zabbix
```

### 1.3 创建目录
* 创建zabbix 日志和pid 存放目录, 并修改目录所有者为zabbix

```bash
[root@localhost zabbix]# mkdir -p /usr/local/zabbix /usr/local/etc/zabbix /var/logs/zabbix /var/run/zabbix 
[root@localhost zabbix]# chown zabbix:zabbix /usr/local/zabbix /usr/local/etc/zabbix /var/logs/zabbix /var/run/zabbix 
```


## 2. 安装
### 2.1 解压
```bash
[root@localhost zabbix]# tar -zxvf zabbix-3.2.7.tar.gz
```
### 2.2 编译
* 进入zabbix 源码目录
* 指定安装目录为 /usr/local/zabbix , 指定配置文件存放目录为: /usr/local/etc/zabbix

```bash
[root@localhost zabbix]# cd zabbix-3.2.7
[root@localhost zabbix-3.2.7]# ./configure --prefix=/usr/local/zabbix --sysconfdir=/usr/local/etc/zabbix --enable-agent
```


### 2.3 安装
* zabbix 客户端安装没有make 操作, 直接make install 即可

```bash
[root@localhost zabbix-3.2.7]#  make install
```

### 2.4 配置
* zabbix_agentd 默认使用配置文件: /usr/local/etc/zabbix/zabbix_agentd.conf
* 修改zabbix_agentd.conf 文件内容
* Hostname 建议名字要有一定的规律

```bash
# 指定pid 文件位置
PidFile=/var/run/zabbix/zabbix_agentd.pid

# 指定日志类型
LogType=file

# 指定日志文件位置
LogFile=/var/logs/zabbix/zabbix_agentd.log

# 指定日志切割大小
LogFileSize=10

# 指定日志级别
DebugLevel=3

# 指定zabbix server 服务地址
Server=192.168.1.100

# 指定zabbix 主动推送服务地址
ServerActive=192.168.1.100

# 指定agent服务占用端口
ListenPort=10050

# 指定agent 主机名称, 必须和zabbix 管理页面中配置的主机名称一致
Hostname=zabbix.100
```

### 2.5 配置zabbix 管理页面

#### 2.5.1 新建主机
* 点击 Configuration-> Hosts -> Create Host
* Host Name: agent 主机名, 即zabbix_agentd.conf 中Hostname配置的值
* Group in groups: 选择已有的组
* New Group : 选择新的分组
* Agent Interface: agent客户端ip和端口
* Enable: 选择启用

![](/assets/zabbix_2017-08-01_190637.png)


#### 2.5.2 选择模板
* No.1 选择模板选项卡
* No.2 输入要关联的模板
* No.3 点击add, No.4 会添加模板列表中
* No.5 点击保存即可.

![](/assets/zabbix_2017-08-01_190851.png)


#### 2.5.3 查看图形
* 点击Monitoring -> Graphs
* 可以选择分组, 主机, 监控项进行查看, 但是每页只能查看一张图标

![](/assets/zabbix_2017-08-01_191853.png)

#### 2.5.4 聚合图形
* 点击Monitoring -> Screens -> Create Screen 创建聚合图形
* 聚合图形可以将多个图形整合在一个页面上
* 聚合只是模板的一个整合而已, 依然可以切换主机的

![](/assets/zabbix_2017-08-01_192443.png)















