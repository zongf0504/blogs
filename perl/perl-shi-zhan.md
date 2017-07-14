# Perl 实战
> 笔者认为Perl语言是最好用的Linux 脚本语言, 笔者会用perl 和 shc 写一些linux 命令.



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