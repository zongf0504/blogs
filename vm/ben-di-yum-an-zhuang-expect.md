# 本地yum 源安装expecct

> 在写一些自动化脚本的时候, 在使用ssh, scp, ftp 等交互命令时, 就不能完全实现自动化, 必须手动输入密码等信息完成交互, 不能实现完全地自动化, 此时就需要借助于expect了. expect 是一款解决交互式命令自动化的工具, 能实现自动化输入等功能. 由于笔者经常写一些自动化的脚本, 所以expect 是笔者必装的软件之一.
> expect 依赖tcl, 需要安装 tcl 和 tcl-devel 依赖, 由于Centos 镜像文件中已经包括了tcl 和 expect 的安装包, 所以笔者就采用本地yum 源方式安装. 关于如何设置本地yum 源, 请参考???;

## 1. 安装

### 1.1 安装tcl

#### 1.1.1 安装tcl
``` bash
[admin@localhost ~]$  sudo yum -y install tcl tcl-devel
``` 

#### 1.1.2 检测安装
``` basah
[admin@localhost yum.repos.d]$ rpm -qa | grep tcl
tcl-8.5.7-6.el6.x86_64
tcl-devel-8.5.7-6.el6.x86_64
```

### 1.2 安装expect
``` bash
[admin@localhost ~]$ sudo yum -y install expect
```