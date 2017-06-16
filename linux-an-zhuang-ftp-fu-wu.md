# Linux 安装FTP 服务

```
[root@localhost ~]# yum -y install vsftpd
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