# 收回root 账号, 新建管理员账号
> 笔者认为,安装完linux 系统之后, 应该第一时间创建管理员用户,尽量避免使用root用户进行操作,这主要是因为root 用户权限过大, 如果不小心执行了一个 rm -rf / 的操作, 那么整个系统就崩溃了. 尤其是在工作中, 大家技术参差不齐, 更应该把root账户收回,为每个人建立起独立的账户.

## 1 新建管理员用户
### 1. 创建管理员用户admin, 并添加到root 组

``` bash
[root@localhost ~] useradd admin
[root@localhost ~] usermod -aG root admin
[root@localhost ~]# passwd admin
Changing password for user admin.
New password: 
BAD PASSWORD: it is too short
BAD PASSWORD: is too simple
Retype new password: 
passwd: all authentication tokens updated successfully.
[root@localhost ~]# id admin
uid=500(admin) gid=500(admin) groups=500(admin),0(root)
```

