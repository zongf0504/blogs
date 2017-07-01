# Linux 安装FTP 服务
> Linux 下的ftp 服务软件有很多, 笔者选择的是vsftpd , 被誉为最安全的FTP 服务. 笔者使用Centos 6.8 系统, 采用yum 方式安装.

# 1. 检测是否安装了vsftpd
如果有输出vsftpd 的相关信息, 则表示已经安装了vsftpd ,否则表示未安装
```
[admin@localhost ~]# rpm -qa | grep vsftpd
vsftpd-2.2.2-24.el6.x86_64
```

# 2. 安装vsftpd
由于vsftpd 软件依赖一些其他的软件和软件库, 所以采用yum 方式安装比较容易.

## 2.1 配置yum 源
* 联网: 联网情况下,不需要其它配置
* 不能联网: 可以配置本地yum源, 可将Centos 系统盘,配置为u pan yum 源

## 2.2 安装vsftpd
对于root 用户之间使用yum -y install vsftpd 安装即可, 对于非root 用户, 如果具有sudo 权限,那么需要使用sudo 命令来安装. sudo yum -y install vsftpd. 使用sudo之后, 你的用户就相当于root 用户了.

```
[admin@localhost ~]# sudo yum -y install vsftpd
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



## 查看安装
```
[root@localhost ~]# rpm -qa | grep vsftpd
vsftpd-2.2.2-24.el6.x86_64
```


## 错误
#### 1. Linux 系统非root 用户登录报错: OOPS: child died
解决方案:
1. 查看SELinux 状态:
```
[root@localhost vsftpd]# sestatus -b | grep ftp
allow_ftpd_anon_write                       off
allow_ftpd_full_access                      off
allow_ftpd_use_cifs                         off
allow_ftpd_use_nfs                          off
ftp_home_dir                                off
ftpd_connect_db                             off
ftpd_use_fusefs                             off
ftpd_use_passive_mode                       off
httpd_enable_ftp_server                     off
tftp_anon_write                             off
tftp_use_cifs                               off
tftp_use_nfs                                off
```
2. 将ftp_home_dir 状态设置为on
```
[root@localhost vsftpd]# setsebool -P ftp_home_dir on
[root@localhost vsftpd]#
```

3. 重启ftp 服务
```
[root@localhost vsftpd]# service vsftpd restart
Shutting down vsftpd:                                      [  OK  ]
Starting vsftpd for vsftpd:                                [  OK  ]
[root@localhost vsftpd]# 
```

### 2. 不能上传文件:vsftpd Could not create file.
1. 确认目录权限是否可写:默认目录为 /var/ftp

2. 查看seLinuxStatus: allow_ftpd_full_access 
```
[root@localhost var]# getsebool -a | grep ftp
allow_ftpd_anon_write --> off
allow_ftpd_full_access --> off
allow_ftpd_use_cifs --> off
allow_ftpd_use_nfs --> off
ftp_home_dir --> on
ftpd_connect_db --> off
ftpd_use_fusefs --> off
ftpd_use_passive_mode --> off
httpd_enable_ftp_server --> off
tftp_anon_write --> off
tftp_use_cifs --> off
tftp_use_nfs --> off
[root@localhost var]# setsebool allow_ftpd_full_access on
```

### 3. linux本地未安装ftp命令
安装ftp命令:
yum -y install ftp



