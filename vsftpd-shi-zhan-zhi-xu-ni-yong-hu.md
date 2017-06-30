# vsftpd 实战配置之虚拟用户配置


# 1. 配置要求

匿名用户:
* 对文件夹具有只读权限 
*

虚拟用户:


# 1. 创建虚拟用户数据源 
## 1.1 创建虚拟用户列表

* 文件格式: 一行用户名, 一行密码.笔者创建文件: vim /etc/vsftpd/vusers.txt

```bash
vftp
vftp@123456
zong
zong@123345
mirror
mirror@123456
```

## 1.2 生成用户名列表db文件

使用db\_load 工具生成用户名列表数据库文件, 文件名必须以.db 结尾

```bash
[root@localhost vsftpd]# db_load -T -t hash -f vusers.list vusers.db 
[root@localhost vsftpd]# ll vusers.db 
-rw-r--r--. 1 root root 12288 Jun 30 15:44 vusers.db
[root@localhost vsftpd]#
```

## 1.2 修改认证方式

1. 查看/etc/vsftpd/vsftpd.conf, 中的pam\_service\_name的值, 笔者的为 vsftpd
2. 编辑/etc/pam.d 目录下的vsftpd 文件, 将原来内容全部注释掉, 然后添加新的auth和account 配置.

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
注意:
* 笔者为64 位centos 系统, 所以是这样配置, 32位系统配置required 和64位不同
* vusers 为虚拟用户名数据库文件, 但是不能写后缀名



#### 2.1.2.4 修改vsftpd 配置

修改配置文件/etc/vsftpd/vsftpd.conf , 文件末尾添加以下内容. 需要注意的是,guest\_username 为虚拟用户代表的系统用户名, 笔者设置为admin用户, 也就是说所有的虚拟用户都相当于admin 用户.

```bash
pam_service_name=vsftpd
# 开启虚拟用户登录
guest_enable=YES
# 设置虚拟用户用户名
guest_username=admin
```

#### 2.1.2.4 创建虚拟用户配置目录

为了给不同的虚拟用户设置不同的权限, 目录, 所以为每个用户创建配置文件. 笔者将虚拟用户配置文件存放在 /etc/vsftpd/vusers\_conf 目录中.

```bash
mkdir /etc/vsftpd/vusers_conf
```

#### 2.1.2.5 创建虚拟用户配置文件

虚拟用户配置文件需要放在虚拟用户配置目录中, 且文件名为用户名.

zong 用户:

```bash
[root@localhost vsftpd]# vim /etc/vsftpd/vusers_conf/zong 
#设置用户根目录  
local_root=/var/data/ftp/soft
#设置虚拟用户具有读写权限
virtual_use_local_privs=YES
```

ftp 用户:

```bash
[root@localhost vsftpd]# vim /etc/vsftpd/vusers_conf/ftp

#设置用户根目录  
local_root=/var/data/ftp/soft
#设置虚拟用户具有读写权限
virtual_use_local_privs=YES
```

# 6. 修改vsftpd 配置

修改配置文件/etc/vsftpd/vsftpd.conf , 文件末尾添加以下内容. 需要注意的是,guest\_username 为虚拟用户代表的系统用户名, 笔者设置为admin用户, 也就是说所有的虚拟用户都相当于admin 用户.

```bash
pam_service_name=vsftpd
# 开启虚拟用户登录
guest_enable=YES
# 设置虚拟用户用户名
guest_username=admin
# 设置用户配置目录
user_config_dir=/etc/vsftpd/vusers_conf
```

# 7. 重启服务器

```bash
[root@localhost vsftpd]# service vsftpd restart
Shutting down vsftpd:                                      [  OK  ]
Starting vsftpd for vsftpd:                                [  OK  ]
[root@localhost vsftpd]#
```



