# vsftp 实战之虚拟用户


我们来搭建一个ftp服务, 供开发组测试组同事使用, 用户及其权限如下:

| 用户角色| 用户名 | 根目录 | 可读 | 上传文件 | 新建文件夹 | 删除/重命名 | 跳出主目录 |
| :--- | :--- | :--- |:--- | :--- | :--- | :--- | :--- |
| 匿名用户 | anonymous | /var/data/ftp/anon | Y | Y | N | N | N |
| 开发组管理员 | developAdmin | /var/data/ftp/develop | Y | Y | Y | Y | N |
| 开发组普通用户 | develop | /var/data/ftp/develop | Y | Y | N | N | N |
| 测试组管理员 | testAdmin | /var/data/ftp/test | Y | Y | Y | Y | N |
| 测试组普通用户 | test | /var/data/ftp/test | Y | Y | N | N | N |
| 超级用户 | superAdmin | /var/data/ftp | Y | Y | Y | Y | Y |

* 用户密码均为: 用户名@123
* 虚拟用户需要一个系统用户作为代理用户,笔者使用admin 用户. 也就是说, 虚拟用户在对文件进行增删改操作时, 相当于admin 用户

# 1 创建用户目录
使用root 用户创建文件夹, 并将文件夹的所有者和所属组修改为admin,因为我设定的虚拟用户代理系统用户为admin , 所以需要admin 用户对这几个根目录有完全的访问权限.但是需要注意的是, ftp 目录所有者和所属组必须为root.
```bash
[root@localhost ~]# mkdir -p /var/data/ftp/anon /var/data/ftp/develop  /var/data/ftp/test
[root@localhost ~]# chown admin:admin /var/data/ftp/anon /var/data/ftp/develop  /var/data/ftp/test
[root@localhost ~]# ll /var/data/ftp/
total 16
drwxr-xr-x. 2 admin admin 4096 Jul  1 19:22 anon
drwxr-xr-x. 2 admin admin 4096 Jul  1 19:22 develop
drwxr-xr-x. 2 admin admin 4096 Jul  1 19:22 test
drwxr-xr-x. 2 admin admin 4096 Jun 20 20:09 wars
[root@localhost ~]# 
```

# 2. 设置数据源

## 2.1 创建用户名密码文件
* 文件位置: /etc/vsftpd/vusers.list
* 文件格式: 一行用户名一行密码:

```bash
[root@localhost ~]# vim /etc/vsftpd/vusers.list
  
developAdmin
developAdmin@123
develop
develop@123
testAdmin
testAdmin@123
test
test@123
superAdmin
superAdmin@123
```

## 2.2 生成用户名密码数据库文件

```bash
[root@localhost ~]# db_load -T -t hash -f /etc/vsftpd/vusers.list  /etc/vsftpd/vusers.db
[root@localhost ~]# ls /etc/vsftpd/
ftpusers   vsftpd.conf             vsftpd.conf.rpmsave  vusers.list
user_list  vsftpd_conf_migrate.sh  vusers.db
[root@localhost ~]# 
```

## 2.3 配置用户数据源
* 编辑文件: /etc/pam.d/vsftpd
* 将原来内容全部注释, 新添加两行, 注意vusers文件不添加后缀名.db
* 对于required 后饮用的库文件, 64位系统和32位系统不一样, 笔者系统为64位操作系统

```bash
[root@localhost vsftpd]# vim /etc/pam.d/vsftpd
#%PAM-1.0
#session    optional     pam_keyinit.so    force revoke
#auth       required    pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
#auth       required    pam_shells.so
#auth       include     password-auth
#account    include     password-auth
#session    required     pam_loginuid.so
#session    include     password-auth

auth required pam_userdb.so db=/etc/vsftpd/vusers
account required pam_userdb.so db=/etc/vsftpd/vusers
```

## 2.4 创建虚拟用户配置目录
* 将不同用户的具体配置文件存放在/etc/vsftpd/vusers_conf 目录下
* 目录中一个用户对应一个文件,文件名为用户名
```bash
[root@localhost ~]# mkdir /etc/vsftpd/vusers_conf
```

## 2.5 为不同用户创建不同的配置文件
为每一个用户创建自己的配置文件, 文件名为用户名
```bash
[root@localhost ~]# touch /etc/vsftpd/vusers_conf/develop
[root@localhost ~]# touch /etc/vsftpd/vusers_conf/developAdmin
[root@localhost ~]# touch /etc/vsftpd/vusers_conf/test
[root@localhost ~]# touch /etc/vsftpd/vusers_conf/testAdmin
[root@localhost ~]# touch /etc/vsftpd/vusers_conf/superAdmin
[root@localhost ~]# ls /etc/vsftpd/vusers_conf/
develop  developAdmin  superAdmin  test  testAdmin
```

** develop **
```bash

```





































































































































































** develop **
```bash

```










































































































































































































































































































































