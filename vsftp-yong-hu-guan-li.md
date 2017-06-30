# Vsftp 用户管理
> vsftpd 服务提供了三种用户类型: 匿名用户, 系统用户, 虚拟用户. 其中系统用户和虚拟用户只能开启一个.


# 虚拟用户
## 1. 创建虚拟用户列表文件
* 文件格式: 一行用户名, 一行密码.笔者创建文件: vim /etc/vsftpd/vusers.txt

```bash
vftp
vftp@123456
```

## 2. 生成用户名列表db文件

使用db_load 工具生成用户名列表数据库文件, 文件名必须以.db 结尾
```bash
[root@localhost vsftpd]# db_load -T -t hash -f vusers.list vusers.db 
[root@localhost vsftpd]# ll vusers.db 
-rw-r--r--. 1 root root 12288 Jun 30 15:44 vusers.db
[root@localhost vsftpd]# 
```

## 3. 修改vsftpd 配置
1. 查看/etc/vsftpd/vsftpd.conf, 中的pam_service_name的值, 笔者的为 vsftpd
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