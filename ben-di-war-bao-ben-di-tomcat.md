# 自动化部署:本地war包, 本地应用服务器

## 1. 新建自由风格的job

1. 点击jenkins -&gt; 新建
2. 输入job名称, 选择'自由风格的项目', 点击OK
   ![](/assets/jenkins_2017-06-016_205126.png)

## 2. 配置General

### 2.1 项目名称

* 这个地方笔者认为叫任务名称更合适, 首先这是一个任务, 其次此名称对应的jenkins 内置变量为 JOB\_NAME.
* 此名称可以修改, 但是不要轻易修改
* 此名称建议以一定格式来命名, 笔者命名格式为: 项目名-job类型-war包获取方式-部署服务器类型

### 2.2 丢弃旧的构建

* 若不勾选此选项, jenkins 会保留所有的构建记录, 当项目多或者构建频繁时, 会占用大量磁盘空间
* 勾选后, 保留构建的天数和保持的最大个数配置一个即可, 笔者选择最大个数为5

### 2.3 参数化过程

* 参数化过程是指配置中的一些变量可接收点击构建时传入的参数.
* 点击添加参数, 选择 String Parameter

| 名字 | 默认值 | 描述 |
| :--- | :--- | :--- |
| warName | LoadBalance | war包名称 |
| warDir | /tmp | war 包所在目录 |
| testUrl | [http://172.22.12.225:7080/LoadBalance/index.jsp](http://172.22.12.225:7080/LoadBalance/index.jsp) | 测试地址 |
| serverHome | /opt/app/tomcat/tomcat-7-7080 | 服务器路径 |
| timeout | 100 | 超时时间\(s\) |

### 2.4 选择jdk 版本

* 当在全局工具中配置了多个jdk时,才有此选择. 默认是第一个jdk

## 3. 构建

此中模式, 只需要在本地执行shell 脚本就行了, 所以点击增加构建步骤, 选择Execute Shell 来添加脚本

### 3.1 获取war包脚本

* 将war包从上传的tmp 目录拷贝到服务器的temp 目录中

```bash
#!/bin/bash
#DESC 获取war 包脚本

#输出日志
echo "[info] begin get project: $warName"

#拷贝war包到服务器的temp 目录中
cp /$warDir/$warName.war $serverHome/temp
```

### 3.2 重部署项目

1. 关闭服务器
2. 删除服务器部署目录中的war包和文件夹
3. 删除服务器工作目录
4. 清空tomcat 输出日志
5. 重新启动服务器
6. 监控服务器是否能启动成功

```bash
#!/bin/bash
#DESC 重新部署项目

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
fi
```

### 3.3 备份项目

* 如果备份文件夹不存在, 则创建备份文件夹
* 如果备份文件夹存在, 则删除上次成功的文件
* 备份文件, 文件名为:warName.第几次构建序号
* 备份文件, 文件名为原war包名称, 可用于恢复文件
* 记录一条成功的记录

```bash
#!/bin/bash
#DESC 部署成功后,备份项目

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



