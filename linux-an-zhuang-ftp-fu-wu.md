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
### 2.4.1 配置文件
vsftpd 服务配置文件默认在/etc/vsftp 目录下
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
vsftp 服务默认根目录为/var/ftp 
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


### 3. linux本地未安装ftp命令
安装ftp命令:
yum -y install ftp



