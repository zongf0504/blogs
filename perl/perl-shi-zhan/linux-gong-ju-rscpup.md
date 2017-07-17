# linux 工具 -- rscpup

> 用于批量从上传文件至远程服务器, 依赖于expect 环境, 需要实现安装expect环境.


## 1. 工具特色

* 文件名支持通配符
* 没有用户交互,自动输入密码
* 可批量上传文件


## 2. rspup 开发

### 2.1 源程序 rspup .pl

```perl


```

### 2.2 加密为二进制程序

* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序, 并删除源程序 
* 将二进制程序rscpdown 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```bash
[admin@localhost perl]$ ls
rspup.pl
[admin@localhost perl]$ perl2bin -d rspup.pl 
开始转换脚本:rspup.pl
[admin@localhost perl]$ mv rspup /usr/local/bin/perl/
```

## 3. 测试

### 3.1 获取帮助信息

* 使用-h 或 --help 选项获取命令的帮助信息

```bash
[admin@localhost test]$ rscpdown -h
Desc: 从远程服务器上批量下载文件,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, 本地目录, "远程文件名,支持通配符", 文件名需要用单引号或双引号包裹
      [expect 绝对路径地址] 可选参数
Exam: rscpdown 192.168.145.100 admin admin . '/tmp/hello*.txt'
      rscpdown 192.168.145.100 admin admin  /tmp '/tmp/hello*.txt' /usr/bin/expect
Auth: zongf
Date: 2017-07-17
[admin@localhost test]$ rscpdown --help
Desc: 从远程服务器上批量下载文件,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, 本地目录, "远程文件名,支持通配符", 文件名需要用单引号或双引号包裹
      [expect 绝对路径地址] 可选参数
Exam: rscpdown 192.168.145.100 admin admin . '/tmp/hello*.txt'
      rscpdown 192.168.145.100 admin admin  /tmp '/tmp/hello*.txt' /usr/bin/expect
Auth: zongf
Date: 2017-07-17
```

### 3.2 从远程服务器批量下载文件
* 命令需要用单引号或者双引号包裹

```bash
[admin@localhost test]$ ./rscpdown 172.22.12.225 admin admin . "/tmp/hello*.txt"
hello1.txt                                                                                                                 100%   12     0.0KB/s   00:00 ETA
hello.txt  
[admin@localhost test]$ ls ./hello*
./hello1.txt  ./hello.txt
```










