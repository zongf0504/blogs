# vsftpd 常用配置

## 匿名用户配置
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


## 虚拟用户配置
常用配置:

| 配置项 | 含义 |
| :--- | :--- |
| guest_enable=YES/NO | 全局配置:是否启用虚拟用户源: YES 启用 NO 不启用 |
| guest_username=user_name | 全局配置:设置虚拟用户代指的系统用户名, 类似于匿名用户的ftp_username |
| local_root=dir_name | 全局配置:设置虚拟用户根目录(若没有为用户专门指定目录的话) |
| user_config_dir=dir_name | 全局配置:设置具体用户特殊配置根目录, 目录下为以用户名命名的配置文件 |
| chroot\_local\_user=YES/NO | 全局配置:设置禁止用户离开主目录\(默认为用户家目录\), 只能在主目录及子目录活动 |
| chroot\_list\_enable=YES/NO | 全局配置:指定特殊用户可以离开主目录, 需要chroot\_local\_user为YES |
| chroot\_list\_file=/etc/vsftpd/chroot\_list | 全局配置:指定允许离开主目录的特殊用户列表文件,需要chroot\_local\_user为YES |
| virtual_use_local_privs=YES/NO | 设定具体虚拟用户权限:是否拥有和代理系统用于同样的权限 |
| write_enable=YES/NO | 设定具体虚拟用户权限:是否拥有写权限 |
| anon_upload_enable=YES/NO | 设定具体虚拟用户权限:可以上传文件 |
| anon_mkdir_write_enable=YES/NO | 具体虚拟用户权限设定:是否可以新建目录 |
| anon_other_write_enable=YES/NO | 具体虚拟用户权限设定:是否可以删除/重命名文件 |
| anon_world_readable_only=YES/NO | 具体虚拟用户权限设定:是否不允许浏览目录下的文件,这个建议设置为NO |




# 