# 本地yum 源安装expecct

> 在写一些自动化脚本的时候, 在使用ssh, scp, ftp 等交互命令时, 就不能完全实现自动化, 必须手动输入密码等信息完成交互, 不能实现完全地自动化, 此时就需要借助于expect了. expect 是一款解决交互式命令自动化的工具, 能实现自动化输入等功能. 由于笔者经常写一些自动化的脚本, 所以expect 是笔者必装的软件之一.
> expect 依赖tcl, 需要安装 tcl 和 tcl-devel 依赖,所以需要先安装tcl 和 tcl-devel. 

## 1. 安装

### 1.1 本地yum 源安装

由于Centos 镜像文件中已经包括了tcl 和 expect 的安装包, 虽然版本有点儿低,但是绝对够用了,而且yum 安装相对来说比较简单,所以笔者就先采用本地yum 源方式安装. 关于如何设置本地yum 源, 请参考???;

#### 1.1.1 安装tcl
``` bash
[admin@localhost ~]$  sudo yum -y install tcl tcl-devel
``` 

### 1.2 安装expect
``` bash
[admin@localhost ~]$ sudo yum -y install expect
```

### 1.3 检测
``` bash
[admin@localhost ~]$ expect -v
expect version 5.44.1.15
```

### 1.2 源码包安装
> TODO

## 2. expect 常用命令
expect 提供了四个常用命令


## 3. expect 使用demo

### 3.1 ssh 自动登录
``` bash
#!/usr/bin/expect -f
spawn ssh root@192.168.145.101
expect "*password:" { send "root\r" }
interact
```

### 3.2 scp 自动下载文件
``` bash
#!/usr/bin/expect -f

spawn scp root@192.168.145.101:/tmp/name.txt .
expect "*password:" { send "root\r" }
interact
```







