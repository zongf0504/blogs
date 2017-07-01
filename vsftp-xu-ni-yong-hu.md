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
注意:
1. 使用root 用户创建文件夹
2. 将新创建文件所有者和所属组修改为admin, 以保证admin用户对此目录有完全访问权限.因为虚拟用户的代理用户为admin.
3. 修改anon 目录权限为777,因为虚拟用户相当于其它人, 所以需要将目录其它人权限设置为7, 这样匿名用户才能进行上传下载文件

```bash
[root@localhost ~]# mkdir -p /var/data/ftp/anon /var/data/ftp/develop  /var/data/ftp/test
[root@localhost ~]# chown admin:admin /var/data/ftp/anon /var/data/ftp/develop  /var/data/ftp/test
[root@localhost ~]# chmod 777 /var/data/ftp/anon
[root@localhost ~]# ll /var/data/ftp/
total 16
drwxrwxrwx. 2 admin admin 4096 Jul  1 19:22 anon
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
### 2.5.1 创建配置文件
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

### 2.5.2 编辑配置文件
* virtual_use_local_privs 如果设置为YES 则表示此用户拥有和代理的系统用户admin 完全一样的权限.因为笔者设置的用户根目录的所有者和所属组均为admin, 所以如果设置了YES 就代表拥有了对这个目录操作的所有权限.

** develop **
```bash
#设定根目录
local_root=/var/data/ftp/develop

# 设定权限,不拥有和代理系统用于同样的权限
virtual_use_local_privs=NO

#设定写权限
write_enable=YES

#设定权限:可以上传文件
anon_upload_enable=YES

#设定权限:不能新建目录
anon_mkdir_write_enable=NO

#设定权限:不能删除/重命名文件
anon_other_write_enable=NO

#设定权限:可以浏览目录下的文件
anon_world_readable_only=NO
```
** developAdmin **
```bash
#设定根目录
local_root=/var/data/ftp/develop

#设置虚拟用户拥有和代理系统用户同样的权限
virtual_use_local_privs=YES
```
** test**
```bash
#设定根目录
local_root=/var/data/ftp/test

# 设定权限,不拥有和代理系统用于同样的权限
virtual_use_local_privs=NO

#设定写权限
write_enable=YES

#设定权限:可以上传文件
anon_upload_enable=YES

#设定权限:不能新建目录
anon_mkdir_write_enable=NO

#设定权限:不能删除/重命名文件
anon_other_write_enable=NO

#设定权限:可以浏览目录下的文件
anon_world_readable_only=NO
```
** testAdmin **
```bash
#设定根目录
local_root=/var/data/ftp/test

#设置虚拟用户拥有和代理系统用户同样的权限
virtual_use_local_privs=YES
```

** superAdmin **
```bash
#设定根目录
local_root=/var/data/ftp

#设置虚拟用户拥有和代理系统用户同样的权限
virtual_use_local_privs=YES
```

# 3. 配置允许用户跳出主目录的用户列表
* 文件位置: /etc/vsftpd/chroot_list
* 文件格式: 一个用户名一行,此处我们只配置superAdmin 用户.

```bash
[root@localhost vsftpd]# vim chroot_list 
superAdmin       
```

# 4. 配置vsftpd 核心配置文件
前面做的都是准备工作, 由/etc/vsftpd/vsftpd.conf 配置文件将前面的操作关联起来

```bash
# Example config file /etc/vsftpd/vsftpd.conf

##########  全局配置  ##########
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=YES

userlist_enable=YES
tcp_wrappers=YES

#pasv 传输方式端口上下限
pasv_min_port=61000
pasv_max_port=62000

##########  匿名用户配置  ##########
#开启匿名用户访问
anonymous_enable=YES
#指定匿名用户访问根目录
anon_root=/var/data/ftp/anon
#匿名用户允许上传文件
anon_upload_enable=YES
#匿名用户不允许创建文件夹
anon_mkdir_write_enable=NO
#匿名用户不允许删除/更名文件/文件夹
anon_other_write_enable=NO
#匿名用户代理系统用户
ftp_username=ftp

##########  虚拟用户配置  ##########
#指定pam 服务名称, 和/etc/pam.d 目录下的文件vsftpd 保持一致
pam_service_name=vsftpd
# 开启虚拟用户登录
guest_enable=YES
# 设置虚拟用户代理系统用户名
guest_username=admin
# 设置默认登录路径
local_root=/var/data/ftp/anon
# 设置运行本地用户登录,不设置的话, 虚拟用户不能登录
local_enable=YES
# 设置具体用户配置文件目录
user_config_dir=/etc/vsftpd/vusers_conf

##########  设置跳出目录   ##########
#设置禁止用户跳出主目录
chroot_local_user=YES
#设置允许特殊用户跳出主目录
chroot_list_enable=YES
#设置特殊用户配置文件
chroot_list_file=/etc/vsftpd/chroot_list

```

# 5. 启动服务器
```bash
[root@localhost vsftpd]# service vsftpd start
Starting vsftpd for vsftpd:                                [  OK  ]
```

#附: 搭建问题
1. 确保防火墙关闭或释放了ftp 相关端口
2. 确保ftp_home_dir, allow_ftpd_full_access, selinux 配置正确
3. 确保






