# Vsftp 用户管理

> vsftpd 提供了简单的用户权限功能, 大体分两大类, 一类是匿名用户, 另一类为具体用户. 具体用户又提供了两种数据源方式, 一种是linux 系统用户作为数据源, 一种是自建虚拟用户作为数据源.

# 1. 匿名用户

## 1.1 默认配置

* 默认情况下, 虚拟用户的根目录为 /var/ftp
* 默认情况下, 匿名用户是可以访问的, 具有只读权限, 不能创建文件夹, 不能删除文件, 不能删除/更名文件
* 默认情况下, 匿名用户使用是系统用户ftp, ftp 为安装vsftpd 过程中创建, 此用户不可登录

## 1.2 常用配置项

* 笔者所写条件允许是指还依赖于两个条件: a\) write\_enable=YES b\) ftp\_username 对该目录写权限

常用配置项:

| 配置项 | 含义 | 默认 |
| :--- | :--- | :--- |
| anonymous\_enable=YES/NO | 设置是否允许匿名用户登录, YES 允许, NO 不允许 | YES |
| anon\_root | 设置匿名用户的根目录，即匿名用户登入后，被定位到此目录下. 此目录所属者和所属主必须为root, 可通过chown root:root 修改 | /var/ftp/ |
| anon\_upload\_enable=YES/NO | 设置是否允许匿名用户上传文件: NO 不允许 YES 条件允许 | NO |
| anon\_mkdir\_write\_enable=YES/NO | 设置是否允许匿名用户创建新目录: NO 不允许 YES 条件允许 | NO |
| anon\_other\_write\_enable=YES/NO | 设置匿名用户是否拥有除了上传文件新建目录的权限\(删除,更名..\) NO 不允许, YES 条件允许 | NO |
| no\_anon\_password=YES/NO | 控制匿名用户登入时是否需要密码，YES不需要，NO需要。 | NO |
| ftp\_username=ftp | 设置匿名用户所使用系统用户名, 默认为不出现在配置中,不建议修改 | ftp |
| chown\_uploads=YES/NO | 是否修改匿名用户上传文件的所有者,不会影响所属组, 默认所有者和所属组均为ftp: NO 否  YES 修改 | NO |
| chown\_username=user\_name | 指定匿名用户上传文件的所有者,用户名必须为存在的系统用户名 | 无 |

# 2. 具体用户

## 2.1 用户源管理

vsftd 的用户数据源只能启用系统用户或虚拟用户中的一种, 不能同时启用.

### 2.1.1 系统用户

* vsftp 默认是允许系统用户登录的, 且登录目录为用户家目录, 并可跳出主目录, 可设置
* vsftp 使用系统用户作为用户源之后, 那么对文件的操作权限就完全依赖于系统用户对文件的读写权限了.

常用配置:

| 配置项 | 含义 |
| :--- | :--- |
| local\_enable=YES/NO | 设置是否允许系统用户登录, YES 允许, NO 不允许 |
| local_root=dir_name | 设置虚拟用户根目录(若没有为用户专门指定目录的话) |
| user_config_dir=dir_name | 设置具体用户特殊配置根目录, 目录下为以用户名命名的配置文件 |
| chroot\_local\_user=YES/NO | 设置禁止用户离开主目录\(默认为用户家目录\), 只能在主目录及子目录活动 |
| chroot\_list\_enable=YES/NO | 指定特殊用户可以离开主目录, 需要chroot\_local\_user为YES |
| chroot\_list\_file=/etc/vsftpd/chroot\_list | 指定允许离开主目录的特殊用户列表文件,需要chroot\_local\_user为YES |


### 2.1.2 虚拟用户

* 虚拟用户是相对于Linux 系统用户而言的, 虚拟用户是指专门为ftp 创建的用户.
* 虚拟用户权限控制没有系统用户那么强大, 但是可以为不同的用户指定特定的配置

常用配置:

| 配置项 | 含义 |
| :--- | :--- |
| guest_enable=YES/NO | 是否启用虚拟用户源: YES 启用  NO 不启用 |
| guest_username=user_name | 设置虚拟用户代指的系统用户名, 类似于匿名用户的ftp_username |
| local_root=dir_name | 设置虚拟用户根目录(若没有为用户专门指定目录的话) |
| user_config_dir=dir_name | 设置具体用户特殊配置根目录, 目录下为以用户名命名的配置文件 |
| chroot\_local\_user=YES/NO | 设置禁止用户离开主目录\(默认为用户家目录\), 只能在主目录及子目录活动 |
| chroot\_list\_enable=YES/NO | 指定特殊用户可以离开主目录, 需要chroot\_local\_user为YES |
| chroot\_list\_file=/etc/vsftpd/chroot\_list | 指定允许离开主目录的特殊用户列表文件,需要chroot\_local\_user为YES |


