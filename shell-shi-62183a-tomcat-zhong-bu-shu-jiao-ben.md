# Shell 实战之Tomcat 重部署

> 在平时的工作中, 重部署应用服务器是经常的事情.部署步骤虽然不麻烦, 但是也是有很多步骤的. 如果每天进行几次重部署, 那么是相当繁琐. 关键在于还没有太大的技术含量. 笔者拿tomcat服务器来举例.

## 1. tomcat 重部署逻辑

笔者认为tomcat重部署应该执行的步骤:  
1. 通过wincp 工具, 将项目war包上传到tomcat 临时目录 temp  
2. 查看当前tomcat 是否在运行, 如果正则运行, 则关闭tomcat  
3. 备份老版本的项目,  
4. 删除老版本项目部署后遗留的文件夹  
5. 删除老版本项目的工作目录  
6. 清空tomcat 的启动日志  
7. 将项目新包从临时目录移动到tomcat部署目录  
8. 重新启动服务器  
9. 监控tomcat 启动日志

## 2. 自动化脚本

```bash
#!/bin/bash
#Desc 重启tomcat服务脚本
#Time Thu Jun 22 22:06:39 CST 2017

#################### 自定义变量 ####################

#定义变量:应用服务器相关文件夹
serverHome=/opt/app/tomcat/tomcat-8-8080
warName=LoadBalance

serverBin=$serverHome/bin
serverLog=$serverHome/logs
serverTemp=$serverHome/temp
serverDeploy=$serverHome/webapps
serverWork=$serverHome/work/Catalina/localhost

#设定运行java环境: jdk
export JAVA_HOME=/opt/app/jdk/jdk1.8.0_131

##################### 执行脚本 #####################
# 0. 检测文件是否存在
if [ ! -f "$serverTemp/$warName.war" ]; then
  echo "[error] The file $serverTemp/$warName.war is not exsits !!!"
  exit 1
else
  echo "[info ] Find file: $serverTemp/$warName.war";
fi

get_pid(){
  pid=`ps -ef | grep -v grep | grep "$serverHome" | awk "{print $2}" `
}

################### 子函数 ###################
#Desc 使用tomcat自带脚本关闭, 1秒关闭一次
shutdown(){
  #执行三次, 每隔1秒执行一次, 尝试正常关闭Tomcat
  for i in 1 2 3
    do 
      if [ -n "$pid" ] ; then 
        echo "[info ] try to shutdown the server ..."
        $serverBin/shutdown.sh &
        # 睡眠3秒,再往下执行
        sleep 3
        get_pid
      fi
    done
}

#Desc 使用linux kill 命令强制关闭服务器, 尝试3次
force_shutdown(){
  for i in 1 2 3
    do 
      if [ -n "$pid" ] ; then 
        echo "[info ] force shutdown the server ..."
        ps -ef | grep -v grep | grep "$serverHome"| awk '{print $2}' | xargs kill -9
        # 睡眠3秒,再往下执行
        sleep 3
        get_pid
      fi
    done
}

# 1. 关闭服务器
#获取当前服务器的进程id
get_pid

# 如果pid 不为空, 则证明已经关闭了
if [ -n "$pid" ] ; then 
  shutdown
else
  echo "[info ] The server is not running !"
fi

# 确认关闭服务器如果还没有关闭, 则强制关闭
if [ -n "$pid" ] ; then
  force_shutdown
fi 

# 最后确认服务器是否关闭, 如果服务器未关闭,则停止重部署流程
if [ -n "$pid" ] ; then 
  echo "[error] cannot shutdown the server,please shutdown manually!"
  exit
fi

# 2. 备份老版本项目
date_time=`date "+%Y%m%d.%H%M"`
mv $serverDeploy/$warName.war $serverDeploy/$warName.war.$date_time

# 3. 删除部署项目的文件夹
echo "[info ] delete the old project: $serverDeploy/$warName"
rm -rf $serverDeploy/$warName

# 4. 清空服务器工作目录
echo "[info ] clean the project work directory: $serverWork/$warName"
rm -rf $serverWork/$warName

# 5. 清空日志文件
echo "[info ] clean the server log: $serverLog/catalina.out"
echo "" > $serverLog/catalina.out

# 6. 将新项目文件移动到服务器部署文件夹中
echo "[info ] deploy the project: $serverTemp/$warName.war"
mv $serverTemp/$warName.war $serverDeploy

# 7. 重新启动服务器
echo "[info ] start the server: $serverBin/startup.sh &"
$serverBin/startup.sh &

# 8. 监控tomcat 启动日志
tail -f $serverLog/catalina.out
```

## 3. 测试输出

写了自动化部署脚本之后, 部署流程就变成了:  
1. 通过wincp 将LoadBalance.war 上传到/opt/wapp/tomcat/tomcat-8-8080/temp 目录下  
2. 登录linux, 执行此脚本

```bash
[admin@localhost tomcat-8-8080]$ ./restart-tomcat.sh 
[info ] Find file: /opt/app/tomcat/tomcat-8-8080/temp/LoadBalance.war
[info ] try to shutdown the server ...
[info ] delete the old project: /opt/app/tomcat/tomcat-8-8080/webapps/LoadBalance
[info ] clean the project work directory: /opt/app/tomcat/tomcat-8-8080/work/Catalina/localhost/LoadBalance
[info ] clean the server log: /opt/app/tomcat/tomcat-8-8080/logs/catalina.out
[info ] deploy the project: /opt/app/tomcat/tomcat-8-8080/temp/LoadBalance.war
[info ] start the server: /opt/app/tomcat/tomcat-8-8080/bin/startup.sh &

Tomcat started.
...
...
```

## 4. 总结

* 这个脚本写的比较麻烦, 但是逻辑相对来说是比较好的
* 对于服务器器关闭写的比较复杂,因为能使用自带shutdown 脚本关闭的话, 就尽量不要使用kill 强制关闭
* 此脚本还可以优化, 可以将重试次数和重试间隔时间做成变量, 记录每次重部署的部署日志等..
* 使用此脚本的时候, 只需要修改 serverHome , warName, JAVA\_HOME 这三个变量就可以了



