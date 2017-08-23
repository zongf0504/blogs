# FastDFS
> fastdfs 是一款轻量级的分布式文件系统。

# 1. 相关简介
## 1. 相关网址
* 源码地址：https://github.com/happyfish100
* 下载地址： http://sourceforge.net/projects/fastdfs/files
* 官方论坛：http://bbs.chinaunix.net/forum-240-1.html

## 2. 相关软件
* libfastcommon-master.zip
* fastdfs
* nginx
* fastdfs-nginx-module
* fastdfs_client_java

#2. Fdfs 单节点安装

## 1. 安装依赖环境
···bash
yum -y install make cmake gcc gcc-c++
```

## 2. 安装libfastcommon
### 1. 下载解压libfastcommon-master.zip
### 2. 编译
···bash
./make.sh 
```
### 3. 安装
```bash
./make.sh install
```

### 4. 创建软连接
···bash
ln -s /usr/local/fdfs/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
	ln -s /usr/local/fdfs/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
```

## 3. 安装FastDfs
### 1. 下载解压
### 2. 修改安装脚本
* vim make.sh

```bash
TARGET_PREFIX=$DESTDIR/usr/local/fdfs
TARGET_CONF_PATH=$DESTDIR/usr/local/etc/fdfs
TARGET_INIT_PATH=$DESTDIR/etc/init.d
```
### 3. 编译
···bash
./make.sh
···

### 4. 安装
```bash
./make.sh install
```

### 5. 查看安装详情：
···bash
[admin@localhost FastDFS]$ ls /usr/local/fdfs/
bin  include  lib  lib64
[admin@localhost FastDFS]$ ls /usr/local/fdfs/bin/
fdfs_appender_test   fdfs_crc32          fdfs_file_info  fdfs_test      fdfs_upload_appender  stop.sh
fdfs_appender_test1  fdfs_delete_file    fdfs_monitor    fdfs_test1     fdfs_upload_file
fdfs_append_file     fdfs_download_file  fdfs_storaged   fdfs_trackerd  restart.sh
[admin@localhost FastDFS]$ ls /usr/local/etc/fdfs/
client.conf.sample  storage.conf.sample  storage_ids.conf.sample  tracker.conf.sample
[admin@localhost FastDFS]$ ls /etc/init.d/fdfs*
/etc/init.d/fdfs_storaged  /etc/init.d/fdfs_trackerd
···

### 6. 添加环境变量
* vim /etc/profile

```bash
#fdfs env
export FDFS_HOME=/usr/local/fdfs
export PATH=$PATH:$FDFS_HOME/bin
"/etc/profile" 107L, 2457C written                                                                                 
[admin@localhost FastDFS]$ source /etc/profile

```

### 7. 修改服务脚本
* vim /etc/rc.d/init.d/fdfs_trackerd,fdfs_storaged

```bash
PRG=/usr/local/bin/fdfs/fdfs_storaged
CONF=/usr/local/etc/fdfs/storage.conf
```

### 8. 修改文件属性
···bash
chown admin:admin /usr/local/etc/fdfs/ -R
```

### 9. 创建软连接
···bash
ln -s /usr/include/fastcommon/ /usr/local/fdfs/include/fastcommon
```

### 10. 拷贝文件
···bash
cp ./conf/http.conf  ./conf/mime.types /usr/local/etc/fdfs/
```

### 11. 创建连接： 
* ln -s /var/data/fdfs/storage/data/ /var/data/fdfs/storage/data/M00

## 4. 配置服务

### 1. 配置跟踪服务器
···bash
#主要修改数据存放目录， ip
bind_addr=192.168.1.10
base_path=/var/data/fdfs/tracker
```

### 2. 配置fdfs服务

## 5. nginx 模块儿安装
### 1. 下载nginx 模块儿

### 2. 编辑模块儿配置文件
···bash
2. 编辑; src/config
ngx_addon_name=ngx_http_fastdfs_module
HTTP_MODULES="$HTTP_MODULES ngx_http_fastdfs_module"
NGX_ADDON_SRCS="$NGX_ADDON_SRCS $ngx_addon_dir/ngx_http_fastdfs_module.c"
CORE_INCS="$CORE_INCS /usr/local/fdfs/include/fastdfs /usr/local/fdfs/include/fastcommon/"
CORE_LIBS="$CORE_LIBS -L/usr/local/lib -lfastcommon -lfdfsclient"
CFLAGS="$CFLAGS -D_FILE_OFFSET_BITS=64 -DFDFS_OUTPUT_CHUNK_SIZE='256*1024' -DFDFS_MOD_CONF_FILENAME='\"/usr/local/etc/fd
fs/mod_fastdfs.conf\"'"

```

### 3. 编译安装nginx


# Java APi
## 1. 下载源码到本地
* 源码地址： https://github.com/happyfish100/fastdfs-client-java.git

## 2. 安装到本地仓库
* mvn clean deploy

## 3. 直接安装jar包
* mvn install:install-file -DgroupId=org.csource -DartifactId=fastdfs-client-java -Dversion=${version} -Dpackaging=jar -Dfile=fastdfs-client-java-${version}.jar

## 4. fdfs 依赖
···xml
<dependency>
	<groupId>org.csource</groupId>
	<artifactId>fastdfs-client-java</artifactId>
	<version>1.27-SNAPSHOT</version>
</dependency>
···

#附：
# 修改公共库
···bash
4. vim /etc/ld.so.conf
   /usr/local/lib
5. ldconfig
···