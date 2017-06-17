# svn war包, 本地tomcat 应用服务器=
> 此种模式也比较简单, 和本地war包差别不大, 将war包从svn服务器上下载到本地之后, 就和本地模式时一样的了.

## 1. 任务配置
点击jenkins ,新建自有风格的任务

### 1. 配置General
1. 配置项目名称: 项目名称一旦确定之后就不要轻易修改了
2. 配置描述: 任务描述, 可按一定规则进行编写
3. 丢弃旧的创建: 指定旧的构建记录删除策略, 默认不删除
4. 参数化构建过程: 自定义参数, 点击构建时可修改. 
5. JDK: 选择jdk 版本

### 1.5 源码管理
1. 配置svn 地址: 
Repository URL 输入war包所在svn服务器父目录的svn 访问地址, 建议一个war包创建一个目录,因为下载时, 会将整个目录中的文件全部下载到本地
![](/assets/jenkins_2017-06-17_075113.png)

2. 配置用户名密码:
如果服务器svn 服务器必须登录的话,选择 Credetials, 如果无可选择的, 则点击add 新增svn 认证信息, 此处使用用户名密码认证策略.
![](/assets/jenkins_2017-06-17_075524.png)

### 2. 构建

#### 2.1 获取war包脚本
由于war熬直接从svn 上下载到了$WORKSPACE目录下, 所以需要将war包从$WORKSPACE目录中拷贝到$warDir 目录.
```bash
#!/bin/bash
#DESC 获取war 包脚本
#PARM 参数化参数: $warName, $warDir, $serverHome

#输出日志
echo "[info] begin get project: $warName"

# 从工作空间中拷贝项目war包到$warDir 目录, 回头用于备份
cp $WORKSPACE/$warName.war $warDir
```

#### 2.2 上传&部署脚本
* 将$warDir中的war包上传到tomcat服务器的temp 目录, 由于是本地,直接使用cp 指令就行了
* 执行重新部署的tomcat 的六步操作
* 检测服务器是否能启动成功

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
   #tomcat服务器在本地时,需要添加此限制   
   BUILD_ID=dontKillMe bash $serverBin/startup.sh
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
[root@localhost backup]# ls ./LB-free-svn-local/
LoadBalance.war  LoadBalance.war.3  SUCCESSBID
```

## 4. 完整配置示例
![](/assets/svn-local.png)







