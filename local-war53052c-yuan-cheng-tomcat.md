# 本地war包, 远程tomcat 应用服务器
> local-remote 模式是指, 项目war包和jenkins 同在一台服务器上, 而要部署的应用服务器tomcat 安装在另外一台linux 服务器上. 这种模式也是比较常用的, 和local-local模式比较相似的,唯一不同的是,部署war包和执行重部署脚本的方式不同. 还有, 远程服务器需要在系统设置中提前配置好.


## 0. 配置远程linux服务器信息
点击 jenkins -> 系统管理 -> 系统设置, 配置 Publish over SSH
* Name: 为服务器取个别名, 笔者习惯命名为: ip@username
* HostName: 域名或ip地址
* Username: 登录用户名
* RemoteDirectory: 远程登录之后,进入的默认目录, 建议写根目录就行
* Passphrase / Password	: 远程登录密码, 这个在高级选项里
![](/assets/jenkins_2017-06-17_064204.png)


## 1. 任务配置
点击jenkins ,新建自有风格的任务

### 1. 配置General
1. 配置项目名称: 项目名称一旦确定之后就不要轻易修改了
2. 配置描述: 任务描述, 可按一定规则进行编写
3. 丢弃旧的创建: 指定旧的构建记录删除策略, 默认不删除
4. 参数化构建过程: 自定义参数, 点击构建时可修改. 
5. JDK: 选择jdk 版本

### 2. 构建

#### 2.1 获取war包脚本
由于war包需要通过wincp 上传到linux 服务器, 所以笔者在使用wincp 上传时, 直接将war包上传到参数$warDir 指定的目录, 即$warDir 目录, 所以此处就不需要货物war包的脚本了

#### 2.2 上传&部署脚本
由于tomcat 在远程linux 服务器, 所以需要将war包上传到远程tomcat 服务器的temp 目录下, 然后让远程服务器执行重新部署tomcat 脚本, 然后监测远程tomcat 是否重新部署成功此处需要借助于 Publish Over SSH Plugin 插件. 点击新增构建步骤-> Send files or execute commonds over SSH.

* Name: 选择要上传或执行脚本的远程Linux服务器, 这个是在系统设置中配置的
* Source files: 要上传的文件, 
* Remove Prefix: 如果Source files 填写的包含路径, 如/tmp/LoadBalance.war, 那么这个地方就建议移除前缀/tmp/ 
* Remote Directory: 远程Linux 服务器的文件夹, 此处为远程tomcat 的temp 目录
* Exec Command: 要执行的远程linux 脚本, 此脚本和local-local 模式基本相同, 唯一不同的就是远程服务器不需要执行: BUILD_ID=dontKillMe bash $serverBin/startup.sh

```bash
#!/bin/bash
#DESC 部署项目
#PARM 参数化参数: serverHome, testUrl, timeout, $warName

#输出日志
echo "[info] begin deploy project: $warName"

##################### 常量定义 #####################
# 应用服务器相关文件夹
serverBin=$serverHome/bin
serverLog=$serverHome/logs
serverTemp=$serverHome/temp
serverDeploy=$serverHome/webapps
serverWork=$serverHome/work/Catalina/localhost


##################### 执行脚本 #####################

# 0.拷贝war包到服务器的temp 目录中
cp $warDir/$warName.war $serverHome/temp

# 1. 关闭服务器
ps -ef | grep -v grep | grep "$serverHome"| awk '{print $2}' | xargs kill -9

# 2. 删除部署项目的war包和文件夹
rm -rf $serverDeploy/$warName
rm -f $serverDeploy/$warName.war

# 3. 清空服务器工作目录
rm -rf $serverWork/$warName

# 4. 清空日志文件
echo "" > $serverLog/catalina.out

# 5. 将新项目文件移动到服务器部署文件夹中
mv $serverTemp/$warName.war $serverDeploy

# 6. 重新启动服务器
$serverBin/startup.sh &


# 7. 监控服务器是否启动成功
cost=0
statusCode=0
while [ $statusCode -ne 200 -a $cost -le $timeout ]  
  do
    statusCode=`curl -o /dev/null -s -w %{http_code} $testUrl`
    echo "cost:$cost ms, statusCode:$statusCode"
    cost=$(( $cost + 5 ))
    sleep 5
  done

if [ $statusCode -ne 200 ] ; then 
   #如果启动不成功则杀死进程
   echo "[faild] shutdown server ..."
   ps -ef | grep -v grep | grep "$serverName" | awk '{print $2}' | xargs kill -9
   exit 1
else
   #服务器启动成功
   exit 0
fi
```

#### 2.3 备份项目脚本
重新部署成功之后, 对新版本进行备份

```bash
#!/bin/bash
#DESC 部署成功后,备份项目
#PARM 参数化参数: $warName, $warDir
#PARM jk内置参数: $JOB_NAME, $BUILD_NUMBER
#PARM 全局自定义: $ITEM_BACKUP, $ITEM_BID_FILE

#输出日志
echo "[info] begin backup project: $warName"

# 备份文件夹
bk_dir=$ITEM_BACKUP/$JOB_NAME

# 创建备份文件夹: 如果文件夹不存在则创建文件夹, 否则删除原来的文件$warName.war
if [ ! -d "$bk_dir" ]; then
  mkdir $bk_dir
else
  rm -f $bk_dir/$warName.war
fi

# 备份文件
mv $warDir/$warName.war $bk_dir
cp $bk_dir/$warName.war $bk_dir/$warName.war.$BUILD_NUMBER

# 记录成功的id
date_time=`date "+%Y%m%d-%H%M"`
echo "$date_time  $BUILD_NUMBER" >> $ITEM_BACKUP/$JOB_NAME/$ITEM_BID_FILE
```

## 3. 测试:
### 3.1 测试
浏览器中输入测试地址, 能看到页面证明部署成. 此时得注意, 看服务器防火墙是否关闭或者测试端口是否释放了, 否则会被防火墙拦截的.

### 3.2 查看备份
通过linux 远程工具登录Linux 服务器, 可以进入备份文件夹, 会发现新增了三个文件
* #warName: 上次重部署成功的war包
* $warName.*: 部署成功的war记录
* SUCCESSBID: 部署成功的记录

```bash
[root@localhost backup]# pwd
/var/data/.jenkins/backup
[root@localhost backup]# ls
[root@localhost backup]# ls
LB-free-local-local
[root@localhost backup]# ls ./LB-free-local-remote/
LoadBalance.war  LoadBalance.war.2  SUCCESSBID
```

## 4. 完整配置示例
![](/assets/local-remote.png)