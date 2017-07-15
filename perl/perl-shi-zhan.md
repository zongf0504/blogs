# Perl 实战
> 笔者认为Perl语言是最好用的Linux 脚本语言, 笔者会用perl 和 shc 写一些linux 命令.


## 1. 常用的linux工具
* 笔者经常用perl + shc + expect 开发一些linux 下的工具程序

| 命令名称 |命令组合 | 功能 |
| :--- |:---- | :---- |
| perl2bin | shc ... | 将perl 程序转换为二进制命令 |
| psgrep | ps -ef \| grep ... | 查询进程 |
| pskill | ps -ef \| grep ...; kill -9 ...; | 强制杀死进程 |
| psnetstat | ps -ef \| grep ...; netstat -tlunp | 查看端口号被占用的进程|
| confview | cat xxx  | 查看配置文件 |
| confgrep | cat xxx | 查看配置文件, 高亮显示 |
| rssh | expect; ssh; | 想远程服务器执行一条命令 |
| rls | expect; ssh; ls; | 获取远程服务器文件列表 | 
| rscpdown | expect; scp; | 从远程服务器下载文件 |
| rscpup | expect; scp; | 上传文件到远程服务器 |


## 2. 环境变量
* 通常情况下, 笔者会将自己开发的程序存放在 /usr/local/bin/perl 目录中, 然后将此目录添加到环境变量中.
* 设置环境变量后, 环境变量目录下的程序在执行的时候就不用输入全路径名称了.

1. 编辑文件:/etc/profile, 文件末尾添加:
```bash
#设置自定义环境变量目录
export PATH=$PATH:/usr/local/bin/perl
```

2. 使设置立即生效: 
```bash
source /etc/profile
```
