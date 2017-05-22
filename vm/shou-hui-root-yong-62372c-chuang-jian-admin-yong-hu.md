# 收回root 账号, 新建管理员账号
> 笔者认为,安装完linux 系统之后, 应该第一时间创建管理员用户,尽量避免使用root用户进行操作,这主要是因为root 用户权限过大, 如果不小心执行了一个 rm -rf / 的操作, 那么整个系统就崩溃了. 尤其是在工作中, 大家技术参差不齐, 更应该把root账户收回,为每个人建立起独立的账户.

## 1. 新建管理员用户
### 1.1 创建管理员用户admin, 并添加到root 组

``` bash
[root@localhost ~] useradd admin
```

### 1.2 设置密码

``` bash
[root@localhost ~]# passwd admin
Changing password for user admin.
New password: 
BAD PASSWORD: it is too short
BAD PASSWORD: is too simple
Retype new password: 
passwd: all authentication tokens updated successfully.

```

### 1.3 将admin 添加到 root 组中

``` bash
[root@localhost ~] usermod -aG root admin
```

### 1.4 查看管理员账号

``` bash
[root@localhost ~]# id admin
uid=500(admin) gid=500(admin) groups=500(admin),0(root)

```

## 2. 修改分区权限

``` bash
[root@localhost ~]# chmod 775 /opt/ /usr/etc /usr/local/ /var/data /var/logs  /var/run
[root@localhost ~]# ll -d /opt/ /usr/etc /usr/local/ /var/data /var/logs  /var/run    
drwxrwxr-x.  7 root root 4096 May 21 06:43 /opt/
drwxrwxr-x.  2 root root 4096 Sep 23  2011 /usr/etc
drwxrwxr-x. 13 root root 4096 May 21 04:50 /usr/local/
drwxrwxr-x.  5 root root 4096 May 21 07:20 /var/data
drwxrwxr-x.  4 root root 4096 May 21 07:24 /var/logs
drwxrwxr-x. 21 root root 4096 May 22 22:13 /var/run
``
## 

``` bash




```

