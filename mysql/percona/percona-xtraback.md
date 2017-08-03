# Xtrabackup 安装
> Xtraback 是percona 提供的用于对mysql数据库进行在线备份,恢复,添加主节点的工具.

## 1. 安装准备

### 1. 安装依赖
* xtrabackup 依赖一些程序, 需要使用yum 安装
* 查看依赖是否安装: rpm -qa cmake gcc gcc-c++ libaio libaio-devel automake autoconf bison libtool ncurses-devel libgcrypt-devel libev-devel libcurl-devel vim-common


```bash
[root@localhost percona]# yum -y install cmake gcc gcc-c++ libaio libaio-devel automake autoconf bison libtool ncurses-devel libgcrypt-devel libev-devel libcurl-devel vim-common
```

### 2. 创建目录


## 2. 安装xtrabackup

### 2.1 下载源码包
* 源码下载: [xtrabackup](https://www.percona.com/downloads/XtraBackup/Percona-XtraBackup-2.3.2/)
```bash
[root@localhost percona]# pwd
/usr/local/src/percona
[root@localhost percona]# wget https://www.percona.com/downloads/XtraBackup/Percona-XtraBackup-2.4.8/source/tarball/percona-xtrabackup-2.4.8.tar.gz
```

### 2.2 解压源码
* 解压源码到当前目录(/usr/local/src/percona)

```bash
[root@localhost percona]# tar -zxvf percona-xtrabackup-2.4.8


```

### 2.3 配置编译环境
* 进入源码目录
* boost 是安装mysql 安装时安装到/usr/local/boost目录的, 详情请参考mysql 源码安装
* 重新编译需要删除当前目录下的文件: CMakeCache.txt 

```bash
[root@localhost percona]#cd percona-xtrabackup-2.4.8
[root@localhost percona-xtrabackup-2.4.8]# cmake \
-DBUILD_CONFIG=xtrabackup_release \
-DWITH_MAN_PAGES=OFF \
-DCMAKE_INSTALL_PREFIX=/usr/local/percona \
-DSYSCONFDIR=/usr/local/etc/percona \
-DWITH_BOOST=/usr/local/boost \
```

### 2.4 编译
```bash
[root@localhost percona-xtrabackup-2.4.8]# make -j4
```

### 2.5 安装
```bash
[root@localhost percona-xtrabackup-2.4.8]# make install
```

### 2.6 查看安装详情
```
[root@localhost percona-xtrabackup-2.4.8]# ls /usr/local/percona/
bin  xtrabackup-test
[root@localhost percona-xtrabackup-2.4.8]# ls /usr/local/percona/bin/
innobackupex  xbcloud  xbcloud_osenv  xbcrypt  xbstream  xtrabackup
```





