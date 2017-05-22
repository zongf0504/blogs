# 收回root 账号, 新建管理员账号

> 笔者认为,安装完linux 系统之后, 应该第一时间创建管理员用户,尽量避免使用root用户进行操作,这主要是因为root 用户权限过大, 如果不小心执行了一个 rm -rf / 的操作, 那么整个系统就崩溃了. 尤其是在工作中, 大家技术参差不齐, 更应该把root账户收回,为每个人建立起独立的账户.

## 1. 新建管理员用户

### 1.1 创建管理员用户admin, 并添加到root 组

```bash
[root@localhost ~]# useradd admin
```

### 1.2 设置密码

```bash
[root@localhost ~]# passwd admin
Changing password for user admin.
New password: 
BAD PASSWORD: it is too short
BAD PASSWORD: is too simple
Retype new password: 
passwd: all authentication tokens updated successfully.
```

### 1.3 将admin 添加到 root 组中

```bash
[root@localhost ~]# usermod -aG root admin
```

### 1.4 查看管理员账号

```bash
[root@localhost ~]# id admin
uid=500(admin) gid=500(admin) groups=500(admin),0(root)
```

## 2. 修改分区权限

```bash
[root@localhost ~]# chmod 775 /opt/ /usr/etc /usr/local/ /var/data /var/logs  /var/run
[root@localhost ~]# ll -d /opt/ /usr/etc /usr/local/ /var/data /var/logs  /var/run    
drwxrwxr-x.  7 root root 4096 May 21 06:43 /opt/
drwxrwxr-x.  2 root root 4096 Sep 23  2011 /usr/etc
drwxrwxr-x. 13 root root 4096 May 21 04:50 /usr/local/
drwxrwxr-x.  5 root root 4096 May 21 07:20 /var/data
drwxrwxr-x.  4 root root 4096 May 21 07:24 /var/logs
drwxrwxr-x. 21 root root 4096 May 22 22:13 /var/run
```

## 3. 修改sudo 文件

```bash
[root@localhost ~]# visudo
```
***
\#\# Sudoers allows particular users to run various commands as
\#\# the root user, without needing the root password.
\#\#
\#\# Examples are provided at the bottom of the file for collections
\#\# of related commands, which can then be delegated out to particular
\#\# users or groups.
\#\# 
\#\# This file must be edited with the 'visudo' command.

\#\# Host Aliases
\#\# Groups of machines. You may prefer to use hostnames (perhaps using 
\#\# wildcards for entire domains) or IP addresses instead.
\# Host_Alias     FILESERVERS = fs1, fs2
\# Host_Alias     MAILSERVERS = smtp, smtp2

\#\# User Aliases
\#\# These aren't often necessary, as you can use regular groups
\#\# (ie, from files, LDAP, NIS, etc) in this file - just use %groupname 
\#\# rather than USERALIAS
\# User_Alias ADMINS = jsmith, mikem


\#\# Command Aliases
\#\# These are groups of related commands...

\#\# Networking
**Cmnd_Alias NETWORKING = /sbin/route, /sbin/ifconfig, /bin/ping, /sbin/dhclient, /usr/bin/net, /sbin/iptables, /usr/bin/rfcomm, /usr/bin/wvdial, /sbin/iwconfig, /sbin/mii-tool **

\#\# Installation and management of software
**Cmnd_Alias SOFTWARE = /bin/rpm, /usr/bin/up2date, /usr/bin/yum **

\#\# Services
**Cmnd_Alias SERVICES = /sbin/service, /sbin/chkconfig **

\#\# Updating the locate database
**Cmnd_Alias LOCATE = /usr/bin/updatedb**

\#\# Storage
**Cmnd_Alias STORAGE = /sbin/fdisk, /sbin/sfdisk, /sbin/parted, /sbin/partprobe, /bin/mount, /bin/umount**

\#\# Delegating permissions
**Cmnd_Alias DELEGATING = /usr/sbin/visudo, /bin/chown, /bin/chmod, /bin/chgrp **

\#\# Processes
**Cmnd_Alias PROCESSES = /bin/nice, /bin/kill, /usr/bin/kill, /usr/bin/killall**

\#\# Drivers
**Cmnd_Alias DRIVERS = /sbin/modprobe**

\# Defaults specification

\#
\# Refuse to run if unable to disable echo on the tty.
\#
Defaults   !visiblepw

\#
\# Preserving HOME has security implications since many programs
\# use it when searching for configuration files. Note that HOME
\# is already set when the the env_reset option is enabled, so
\# this option is only effective for configurations where either
\# env_reset is disabled or HOME is present in the env_keep list.
\#
Defaults    always_set_home

Defaults    env_reset
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS"
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"

\#
\# Adding HOME to env_keep may enable a user to run unrestricted
\# commands via sudo.
\#
\# Defaults   env_keep += "HOME"

Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin

\#\# Next comes the main part: which users can run what software on 
\#\# which machines (the sudoers file can be shared between multiple
\#\# systems).
\#\# Syntax:
\#\#
\#\#      user    MACHINE=COMMANDS
\#\#
\#\# The COMMANDS section may have other options added to it.
\#\#
\#\# Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL

\#\# Allows members of the 'sys' group to run networking, software, 
\#\# service management apps and more.
**admin ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS**

\#\# Allows people in group wheel to run all commands
\# %wheel        ALL=(ALL)       ALL

\#\# Same thing without a password
\# %wheel        ALL=(ALL)       NOPASSWD: ALL

\#\# Allows members of the users group to mount and unmount the 
\#\# cdrom as root
%root  ALL=/bin/mount, /bin/umount /mnt/cdrom

\#\# Allows members of the users group to shutdown this system
\# %users  localhost=/sbin/shutdown -h now

\#\# Read drop-in files from /etc/sudoers.d (the \# here does not mean a comment)
\#includedir /etc/sudoers.d

***

## 4. 测试

### 4.1 使用admin 用户登录服务器,查看防火墙状态
默认情况下, admin 是不能执行 service命令的, 纵使dmin 添加到了root 组中.
``` bash

[admin@localhost ~]$ service iptables status
iptables: Only usable by root.

```

### 4.2 使用 sudo 命令查看
第一次使用sudo 时,需要输入 admin 密码, 此时就可以使用service 命令了

``` bash
[admin@localhost ~]$ sudo service iptables status
[sudo] password for admin: 
Table: filter
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22 
5    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination         

[admin@localhost ~]$
```



