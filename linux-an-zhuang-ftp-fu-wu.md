# Linux 安装FTP 服务
> Linux 下的ftp 服务软件有很多, 笔者选择的是vsftpd , 被誉为最安全的FTP 服务. 笔者使用Centos 6.8 系统, 采用yum 方式安装.

# 1. 检测是否安装了vsftpd
如果有输出vsftpd 的相关信息, 则表示已经安装了vsftpd ,否则表示未安装
```
[root@localhost ~]# rpm -qa | grep vsftpd
vsftpd-2.2.2-24.el6.x86_64
```

# 2. 安装vsftpd
由于vsftpd 软件依赖一些其他的软件和软件库, 所以采用yum 方式安装比较容易.

## 2.1 配置yum 源
* 联网: 联网情况下,不需要其它配置
* 不能联网: 可以配置本地yum源, 可将Centos 系统盘,配置为u pan yum 源

## 2.2 安装vsftpd
对于使用yum 方式安装软件,通常需要使用root 用户才能安装.安装命令: yum -y install vsftpd

```
[root@localhost ~]#  yum -y install vsftpd
Loaded plugins: fastestmirror, security
Setting up Install Process
Determining fastest mirrors
 * base: centos.ustc.edu.cn
 * extras: centos.ustc.edu.cn
 * updates: mirror.bit.edu.cn
base                                                                                                  | 3.7 kB     00:00     
base/primary_db                                                                                       | 4.7 MB     00:01     
extras                                                                                                | 3.4 kB     00:00     
extras/primary_db                                                                                     |  29 kB     00:00     
updates                                                                                               | 3.4 kB     00:00     
updates/primary_db                                                                                    | 1.4 MB     00:00     
Resolving Dependencies
--> Running transaction check
---> Package vsftpd.x86_64 0:2.2.2-24.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=============================================================================================================================
 Package                     Arch                        Version                             Repository                 Size
=============================================================================================================================
Installing:
 vsftpd                      x86_64                      2.2.2-24.el6                        base                      156 k

Transaction Summary
=============================================================================================================================
Install       1 Package(s)

Total download size: 156 k
Installed size: 340 k
Downloading Packages:
vsftpd-2.2.2-24.el6.x86_64.rpm                                                                        | 156 kB     00:00     
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : vsftpd-2.2.2-24.el6.x86_64                                                                                1/1 
  Verifying  : vsftpd-2.2.2-24.el6.x86_64                                                                                1/1 

Installed:
  vsftpd.x86_64 0:2.2.2-24.el6                                                                                               

Complete!
```

## 2.3 查看安装版本

```
[root @localhost ~]# vsftpd -v
vsftpd: version 2.2.2
```

## 2.4 默认配置
### 2.4.1 配置文件位置
vsftpd 服务配置文件默认在/etc/vsftp 目录下, 核心配置文件为vsftpd.conf.
```bash
[root@localhost ~]# ll /etc/vsftpd/
total 28
-rw-------. 1 root root  125 May 11  2016 ftpusers
-rw-------. 1 root root  361 May 11  2016 user_list
-rw-------. 1 root root 4599 May 11  2016 vsftpd.conf
-rwxr--r--. 1 root root  338 May 11  2016 vsftpd_conf_migrate.sh
-rw-------. 1 root root 4647 Jun 20 20:07 vsftpd.conf.rpmsave
[root@localhost ~]# 
```

### 2.4.2 默认根目录
vsftp 服务默认根目录为/var/ftp, 此目录所属者和所属组都是root.
```bash
[root@localhost ~]# ll -d /var/ftp/
drwxr-xr-x. 3 root root 4096 Jul  1 16:58 /var/ftp/
[root@localhost ~]# ll /var/ftp/
total 4
drwxr-xr-x. 2 root root 4096 May 11  2016 pub
[root@localhost ~]# 
```

### 2.4.3 默认匿名用户
vsftpd 安装过程中会创建ftp 用户作为匿名用户的代理用户,ftp 用户不能登录系统.
```bash
[root@localhost ~]# id ftp
uid=14(ftp) gid=50(ftp) groups=50(ftp)
[root@localhost ~]# cat /etc/passwd | grep ftp
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
[root@localhost ~]# 
```

### 2.4.5 默认权限
默认配置下, vsftpd 服务允许匿名用户访问, 使用Linux 系统用户作为用户源, 允许系统用户登录.
* 匿名用户权限: 根目录/var/ftp, 可读, 可下载, 不可上传文件, 不可新建文件夹, 不可删除/更名文件
* 系统用户权限: 根目录为用户家目录,可跳出用户家目录, 对文件的权限由linux用户权限控制.

# 3. 系统配置
安装vsftpd 之后, 需要对系统做一些修改配置
* ftp_home_dir: 解决非root 用户登录报错: OOPS: child died
* allow_ftpd_full_access: 解决不能上传文件问题

```bash
[root@localhost vsftpd] setsebool -P ftp_home_dir on
[root@localhost vsftpd] setsebool allow_ftpd_full_access on
```

# 4. 服务器启动
Centos 系列可通过service 命令进行服务器的启动, 停止, 重启

## 4.1 启动服务器
```bash
[root@localhost ~]# service vsftpd start
Starting vsftpd for vsftpd:                                [  OK  ]
[root@localhost ~]# 
```

## 4.2 重启服务器
```bash
[root@localhost ~]# service vsftpd restart
Shutting down vsftpd:                                      [  OK  ]
Starting vsftpd for vsftpd:                                [  OK  ]
[root@localhost ~]#
```

## 4.3 停止服务器
```bash
[root@localhost ~]# service vsftpd stop
Shutting down vsftpd:                                      [  OK  ]
[root@localhost ~]# 
```

## 4.4 设置开机自启
可以选择将vsftpd服务设置为开机自启, 设置方式可以使用chkconfig 命令, 也可以自定义启动脚本.笔者使用chkconfig 命令. chkconfig 可以对linux 的其中运行级别分别设置开机启动.

* 0：表示关机
* 1：单用户模式
* 2：无网络连接的多用户命令行模式
* 3：有网络连接的多用户命令行模式
* 4：不可用
* 5：带图形界面的多用户模式
* 6：重新启动

### 4.4.1 查看vsftpd 服务开机启动状态
```bash
[root@localhost ~]# chkconfig | grep vsftpd
vsftpd          0:off   1:off   2:off   3:off   4:off   5:off   6:off
[root@localhost ~]# 
```

### 4.4.2 修改vsftpd 开机启动
* 我们只设置开机级别为35 的时候,自动启动vsftpd 服务即可.

```bash
[root@localhost ~]# chkconfig --level 35 vsftpd on
[root@localhost ~]# chkconfig | grep vsftpd
vsftpd          0:off   1:off   2:off   3:on    4:off   5:on    6:off
[root@localhost ~]# 
```

# 5. vsftpd 防火墙设置
* vsftpd服务默认监听20和21端口, 其它电脑要想访问,那么需要释放防火墙端口或关闭防火墙.不推荐关闭防火墙方式.
* vsftpd 传输数据默认使用PASV安全模式,所以需要设置PASV端口上下限,并释放端口

## 5.1 设定PASV 端口上下限
编辑配置文件: /etc/vsftpd/vsftpd.conf, 文件末尾追加两行:
```bash
#设定PASV 端口下限
pasv_min_port=61000
#设定PASV 端口上限
pasv_max_port=62000
```
## 5.2 释放防火墙端口
编辑配置文件: /etc/sysconfig/iptables, 文件中添加以下配置:
```bash
-A INPUT -m state --state NEW -m tcp -p tcp --dport 20 -j ACCEPT
-A OUTPUT -m state --state NEW -m tcp -p tcp --dport 20 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
-A OUTPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 61000:62000 -j ACCEPT
-A OUTPUT -m state --state NEW -m tcp -p tcp --dport 61000:62000 -j ACCEPT
```
## 5.3 重启服务
重启vsftpd服务和防火墙
```bash
[root@localhost ~]# service vsftpd restart
Shutting down vsftpd:                                      [  OK  ]
Starting vsftpd for vsftpd:                                [  OK  ]
[root@localhost ~]# service iptables restart
iptables: Setting chains to policy ACCEPT: filter          [  OK  ]
iptables: Flushing firewall rules:                         [  OK  ]
iptables: Unloading modules:                               [  OK  ]
iptables: Applying firewall rules:                         [  OK  ]
[root@localhost ~]# 
```

# 6. 测试

## 6.1 浏览器访问
直接输入地址ftp://192.168.145.100/ 即可
![](/assets/ftp_2017-07-01_102426.png)

## 6.2 windows 文件系统访问
文件路径中输入地址: ftp://192.168.145.100/
![](/assets/ftp_2017-07-01_102339.png)

## 6.3 FTP 客户端工具wincp 访问
新建站点,选择ftp类型, 输入地址, 勾选匿名登录
![](/assets/ftp_2017-07-01_102506.png)

## 6.4 ftp 命令访问
* 需要本地安装ftp 命令, windows自带ftp 命令, 有些linux 服务器也自带ftp 命令, 如果没有的话,则需要安装ftp 命令.安装方式: yum -y install ftp
* 匿名用户访问,需要手工输入用户名为 anonymous

```bash
[root@localhost ~]# ftp 192.168.145.100
Connected to 192.168.145.100 (192.168.145.100).
220 (vsFTPd 2.2.2)
Name (192.168.145.100:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
227 Entering Passive Mode (192,168,145,100,238,253).
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 May 11  2016 pub
226 Directory send OK.
ftp> quit
221 Goodbye.
[root@localhost ~]# 
```




