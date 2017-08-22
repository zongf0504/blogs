#Nginx 监控Zabbix
> Zabbix 可以用于监控Nginx服务器连接数,请求数,是否存活等状态,监控起来还算是比较简单的. 但是需要在nginx 所在服务器上安装Zabbix_agentd .


## 1. Nginx 开启监控状态功能
* nginx 需要开启状态查询的配置

```bash
location /status {
     stub_status on;
     access_log off;
}
```

* 重启nginx服务器, 并测试

```bash
[root@localhost ~]# nginx -s reload
[root@localhost ~]# curl http://172.22.12.100/status  
Active connections: 3 
server accepts handled requests
 454 454 6886 
Reading: 0 Writing: 1 Waiting: 2 
```

## 2. 编写zabbix 监控脚本
* 笔者zabbix_agentd 的自定义监控脚本存放在/usr/local/etc/zabbix/scripts 中
* 创建监控脚本: vim /usr/local/etc/zabbix/scripts/ngx_status.sh

```bash
#!/bin/bash
#Desc 监控nginx 状态脚本
#Auth zongf
#Date 2017/08/22
 
URL="http://172.22.12.100:80/status/"
 
# 检测nginx进程是否存在
function ping {
    /sbin/pidof nginx | wc -l 
}
# 检测nginx性能
function active {
    curl $URL 2>/dev/null| grep 'Active' | awk '{print $NF}'
}
function reading {
    curl $URL 2>/dev/null| grep 'Reading' | awk '{print $2}'
}
function writing {
    curl $URL 2>/dev/null| grep 'Writing' | awk '{print $4}'
}
function waiting {
    curl $URL 2>/dev/null| grep 'Waiting' | awk '{print $6}'
}
function accepts {
    curl $URL 2>/dev/null| awk NR==3 | awk '{print $1}'
}
function handled {
    curl $URL 2>/dev/null| awk NR==3 | awk '{print $2}'
}
function requests {
    curl $URL 2>/dev/null| awk NR==3 | awk '{print $3}'
}
# 执行function
$1
```

## 4. 测试监控脚本
* 非必须步骤, 但是建议测试一下

```bash
[root@localhost scripts]# pwd
/usr/local/etc/zabbix/scripts
[root@localhost scripts]# ./ngx_status.sh ping  
1
[root@localhost scripts]# ./ngx_status.sh accepts
550
```

## 5. 配置zabbix_agentd
* 编辑配置文件 /usr/local/etc/zabbix/zabbix_agentd.conf , 添加以下配置:

```bash
#monitor nginx
UserParameter=nginx.status[*],/usr/local/etc/zabbix/scripts/ngx_status.sh $1
```

## 6. 重启zabbix_agentd 

```bash
[root@localhost ~]# killall zabbix_agentd
[root@localhost ~]# zabbix_agentd 
```

## 7. 测试脚本
* 非必须步骤, 但是建议测试一下 

```bash
[root@localhost scripts]# zabbix_get -s 172.22.12.100 -k 'nginx.status[ping]'
1
[root@localhost scripts]# zabbix_get -s 172.22.12.100 -k 'nginx.status[accepts]'
562
```

## 8. 导入nginx 监控模板
* 如果之前导入过, 就无须再导入了, 直接进行主机管理模板即可
* 登录zabbix web, 点击 Configuration -> Templates -> import, 选择模板文件
* 模板文件内容见附录

## 9. 主机添加nginx 监控模板
* 登录zabbix, 点击Configuration -> Hosts -> 主机 -> Templates 
* 选择要关联的模板, 点击add, 点击update 即可
![](/assets/zabbix_nginx_2017-08-22_145135.png)

## 10. 创建监控Screen
* 点击Monitoring -> Screens, 新创建监控Screen 
![](/assets/zabbix_nginx_2017-08-22_145908.png)




