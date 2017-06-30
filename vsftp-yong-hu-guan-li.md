# Vsftp 用户管理

> vsftpd 服务提供了三种用户类型: 匿名用户, 系统用户, 虚拟用户. 其中系统用户和虚拟用户只能开启一个.



# 1. 匿名用户

## 1.1 默认配置
* 默认情况下, 虚拟用户的根目录为 /var/ftp
* 默认情况下, 匿名用户是可以访问的, 具有只读权限, 不能创建文件夹, 不能删除文件, 不能删除/更名文件
* 默认情况下, 匿名用户使用是系统用户ftp, ftp 为安装vsftpd 过程中创建, 此用户不可登录



## 1.2 常用配置项
* 笔者所写条件允许是指还依赖于两个条件: a) write_enable=YES b) ftp_username 对该目录写权限

常用配置项:

| 配置项 | 含义 | 默认 |
| :--- | :--- | :--- |
| anonymous_enable=YES/NO | 设置是否允许匿名用户登录, YES 允许, NO 不允许 | YES |
| anon_root | 设置匿名用户的根目录，即匿名用户登入后，被定位到此目录下. 此目录所属者和所属主必须为root, 可通过chown root:root 修改 | /var/ftp/ |
| anon_upload_enable=YES/NO | 设置是否允许匿名用户上传文件: NO 不允许 YES 条件允许| NO |
| anon_mkdir_write_enable=YES/NO |设置是否允许匿名用户创建新目录: NO 不允许 YES 条件允许| NO |
| anon_other_write_enable=YES/NO | 设置匿名用户是否拥有除了上传文件新建目录的权限(删除,更名..) NO 不允许, YES 条件允许 | NO |
| no_anon_password=YES/NO | 控制匿名用户登入时是否需要密码，YES不需要，NO需要。 | NO |
| ftp_username=ftp | 设置匿名用户所使用系统用户名, 默认为不出现在配置中,不建议修改 | ftp |
| chown_uploads=YES/NO | 是否修改匿名用户上传文件的所有者,不会影响所属组, 默认所有者和所属组均为ftp: NO 否  YES 修改 | NO |
| chown_username=user_name | 指定匿名用户上传文件的所有者,用户名必须为存在的系统用户名 | 无 |

## 1.3 推荐配置
通常对于匿名用户, 分配以下权限即可
* 允许匿名用户访问
* 匿名用户访问特殊文件夹
* 匿名用于允许上传文件
* 匿名用户不允许新建文件夹
* 匿名用户不允许删除或修改文件名

```bash
#允许匿名用户访问
anonymous_enable=YES
#指定匿名用户访问根目录
anon_root=/var/data/ftp
#匿名用户允许上传文件
anon_upload_enable=YES
#匿名用户不允许创建文件夹
anon_mkdir_write_enable=NO
#匿名用户不允许删除/更名文件/文件夹
anon_other_write_enable=NO
```
* 需要注意, 匿名用户ftp 需要对文件夹/var/data/ftp 具有写权限, 才能进行上传文件




## 1. 修改虚拟用户的访问根目录

配置文件: /etc/vsftp/vsftpd.conf 添加配置, 这样匿名用户登录之后, 就会进入/var/data/ftp 目录.

```bash
anon_root=/var/data/ftp
```

## 2.

# 系统用户

# 虚拟用户

## 1. 创建虚拟用户列表文件

* 文件格式: 一行用户名, 一行密码.笔者创建文件: vim /etc/vsftpd/vusers.txt

```bash
vftp
vftp@123456
zong
zong@123345
```

## 2. 生成用户名列表db文件

使用db\_load 工具生成用户名列表数据库文件, 文件名必须以.db 结尾

```bash
[root@localhost vsftpd]# db_load -T -t hash -f vusers.list vusers.db 
[root@localhost vsftpd]# ll vusers.db 
-rw-r--r--. 1 root root 12288 Jun 30 15:44 vusers.db
[root@localhost vsftpd]#
```

## 3. 修改vsftpd 配置

1. 查看/etc/vsftpd/vsftpd.conf, 中的pam\_service\_name的值, 笔者的为 vsftpd
2. 编辑/etc/pam.d 目录下的vsftpd 文件, 将原来内容全部注释掉, 然后添加新的auth和account 配置.

注意:

* 笔者为64 位centos 系统, 所以是这样配置, 32位系统配置required 和64位不同
* vusers 为虚拟用户名数据库文件, 但是不能写后缀名

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

## 4. 创建虚拟用户配置目录

为了给不同的虚拟用户设置不同的权限, 目录, 所以为每个用户创建配置文件. 笔者将虚拟用户配置文件存放在 /etc/vsftpd/vusers\_conf 目录中.

```bash
mkdir /etc/vsftpd/vusers_conf
```

## 5. 创建虚拟用户配置文件

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



