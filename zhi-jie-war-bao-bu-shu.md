# jenkins 直接war包部署

> jenkins 直接war包部署是指, 直接由开发同事提供编译好的war包, 直接部署到应用服务器\(如tomcat\)即可. 此种方式相对比较简单, 也比较常用, 通常这种情况, jenkins job 类型选择 '构建一个自由风格的软件项目' 即可.但是, 根据war包来源和服务器位置,也可衍生出很多组合.

## 1. 直接war包模式:
| 模式 | 说明 |
| :--- | :--- |
| local-local | jenkins 和 tomcat 安装在同一台Linux服务器上, war包通过wincp 工具上传 |
| http-local | jenkins 和 tomcat 安装在同一台Linux服务器上, war包存放在web 服务上 |
| ftp-local | jenkins 和 tomcat 安装在同一台Linux服务器上, war包存放在ftp 服务器上 |
| svn-local | jenkins 和 tomcat 安装在同一台Linux服务器上, war包存放在svn 服务器上 |
| sftp-local | jenkins 和 tomcat 安装在同一台Linux服务器上, war包存放在其它Linux服务器 |
| local-remote | jenkins 和 tomcat 安装在不同Linux服务器上, war包通过wincp 工具上传 |
| http-remote | jenkins 和 tomcat 安装在不同Linux服务器上, war包存放在web 服务上 |
| ftp-remote | jenkins 和 tomcat 安装在不同Linux服务器上, war包存放在ftp 服务器上 |
| svn-remote | jenkins 和 tomcat 安装在不同Linux服务器上, war包存放在svn 服务器上 |
| sftp-remote | jenkins 和 tomcat 安装在不同Linux服务器上, war包存放在其它Linux服务器上 |

* 本地应用服务器: 应用服务器\(如tomcat\)和jenkins在同一台服务器上, 通过写本地脚本即可实现重部署
* 远程应哟服务器: 应用服务器\(如tomcat\)和jenkins不在同一台linux服务器上, 需要借助Publish Over SSH Plugin 插件进行war包上传, 执行远程服务器上的重部署脚本

## 2. 直接war包模式部署配置概述
### 2.1 创建自由风格的软件项目

1. 点击jenkins -&gt; 新建
2. 输入jenkins 任务名称,
3. 选择'构建一个自由风格的软件项目'
4. 勾选Add to current view\(添加到当前视图\), 可选步骤
5. 点击OK后, 跳转到配置页面

对于jenkins 任务名称, 有以下几点建议:

* 虽然命名可修改, 但一旦确定, 不建议轻易修改, 因为修改之后会对工作空间等均做修改.
* 命名有规范,按一定格式命名, 建议包含视图简称前缀
* 笔者写命名规范: 视图为项目名称, job 命名为: 视图简称-任务风格-war包获取类型-服务器类型, 这个仅仅是在写demo中使用, 在工作环境中不完全相同.
  ![](/assets/jenkins_2017-06-17_063136.png)

### 2.2 自由风格配置模块

自由风格任务,通常包含以下六块儿配置,   
![](/assets/jenkins_2017-06-17_071339.png)

#### 2.2.1 General 配置

1. 项目名称: 笔者认为应该叫job 名称更好, 因为第一它是jenkins 任务的名称, 第二jenkins 用于表示此变量的内置变量为$JOB\_NAME, 一旦确定好就不要随意修改.
2. 描述: 对此任务的详细描述, 最好以一定格式书写, 因为可在任务列表中显示
3. 丢弃旧的构建: 配置对构建记录的删除规则, 默认为不删除.在配置任务多,或构建频繁的情况, 不删除会占用大量磁盘空间, 因此建议配置. 笔者通常只配置保持构建的最大个数
4. 参数化过程: 配置一些可选参数, 在点击构建任务时, 可对参数进行修改
5. 关闭构建: 不能勾选, 勾选了这个任务就不能执行了
6. 在必要的时候并非构建: 勾选表示只要点击构建按钮, 无论是否有job 正则构建, 都会立刻进行构建,这样会导致并非构建, 对服务器性能,和tomcat占用内存都有要求; 不勾选表示, 点击按钮后, 如果没有任务正则构建, 则立刻进行构建, 否则等任务结束后进行构建. 笔者通常会选择不勾选
7. JDK: 选择jdk版本, 当在系统管理-&gt;Global Tool Configuration 配置多个JDK时才会有此选项,默认选中配置的第一个jdk, 因此在配置jdk时,建议将默认使用的jdk 配置为第一个. 此选项对于部署本地tomcat服务器有意义, 会影响启动tomcat 使用的jdk.
8. 高级: 点击高级按钮, 还可以配置重试次数, 自定义工作空间等, 笔者通常都不配置这些.

![](/assets/jenkins_2017-06-20_165142.png)

#### 2.2.2 源码管理

根据war包是否在svn/git服务器上,此配置模块儿有两种方式

#### 2.2.2.1 从svn 服务器上获取war包

1. 配置svn 地址:   
   Repository URL 输入war包所在svn服务器父目录的svn 访问地址, 建议一个war包创建一个目录,因为下载时, 会将整个目录中的文件全部下载到本地  
   ![](/assets/jenkins_2017-06-17_075113.png)

2. 配置用户名密码:  
   如果服务器svn 服务器必须登录的话,选择 Credetials, 如果无可选择的, 则点击add 新增svn 认证信息, 此处使用用户名密码认证策略.  
   ![](/assets/jenkins_2017-06-17_075524.png)

#### 2.2.2.2 不从svn/git 服务器上获取war包

不从svn 服务器上获取war包, 配置为None 即可  
![](/assets/jenkins_2017-06-17_074903.png)

### 2.2.3 构建触发器

触发器是用来定时自动触发jenkins任务的, 通过触发器, 我们可以轻松实现定时重部署的操作.笔者常用的触发器有两种.定时表达式是基于cron 表达式的, 唯一不同时, 使用H 开头的话, jenkins 会根据任务名称hash 出一个时间, 以防止不同的任务在同一时间段同时触发. 如果想要精确触发时间, 那么不使用H , 使用原生的cron 表达式即可.

#### 2.2.3.1 Build periodcally: 定时触发任务

对于自动化部署, 定时触发任务还是比较常用的.比如说,每天早上8点进行一次自动重部署.

示例: 每天早上7点到8点之间执行一次  
![](/assets/jenkins_2017-06-17_082127.png)

#### 2.2.3.2 Poll SCM:定时检测svn/git 目录是否有更新, 如果有更新, 则触发任务

此种方式是使用限制的, 通常并不常用

* war包获取方式为svn/git
* 若为svn方式的话, svn地址必须为[http://xxx](http://xxx) 方式, 不能为svn://xxx方式, 如果是svn 开头,那么无论是否有变化都会执行此任务, 这貌似是jenkins 的一个解决不了的bug
* 对于自动化部署, 这种方式通常并不适用, 因为只要有提交则重新部署, 当分批次提交时, 就会有多次部署, 不合理.
* 对于定时发布mvn 模块儿可以使用

示例:每五分钟主动检测是否有更新  
![](/assets/jenkins_2017-06-17_082149.png)

### 2.2.4 构建环境

在开始构建之前对远程服务器做一些初始化操作, 笔者通常不配置此模块儿  
![](/assets/jenkins_2017-06-17_085525.png)

### 2.2.5 构建

#### 2.2.5.1 获取war包

* 对于本地war包, ftp 包, sftp 包都是通过执行本地脚本来获取, 因此构建步骤选择Execute Shell 即可.
* 点击增加构建步骤-&gt; Execute shell
  ![](/assets/jenkins_2017-06-17_083923.png)

#### 2.2.5.2 部署项目

根据应用服务器\(tomcat\)所在服务器位置不同, 此步骤分两种情况

##### 2.2.5.2.1 本地应用服务器\(tomcat\)

本地tomcat 依然是执行本地脚本即可, 因此构建步骤还是选择Execute Shell  
![](/assets/jenkins_2017-06-17_085024.png)

##### 2.2.5.2.2 远程应用服务器\(tomcat\)

远程Linux服务器上的tomcat, 需要借助Publish Over SSH Plugin 插件来进行远程部署

* Name: 系统配置中,Publish SSH 模块配置的服务器
* Source files: war包所在位置
* Remove prefix: 移除war包目录前缀
* Remote Directory: war包上传到服务器的位置
* Exec command: 远程服务器执行的脚本命令
![](/assets/jenkins_2017-06-20_165516.png)

#### 2.2.5.3 备份项目

笔者习惯于将成功部署后的war包记录归档, 并简单记录日志,所以部署成功之后, 还会执行一个备份脚本.由于备份脚本是在本地执行的, 所以构建步骤选择 Execute shell 即可  

备份脚本:
```bash
#!/bin/bash
#DESC 部署成功后,备份项目
#PARM 参数化参数: $warName, $warDir
#PARM jk内置参数: $JOB_NAME, $BUILD_NUMBER
#PARM 全局自定义: $ITEM_BACKUP, $ITEM_BID_FILE

#输出日志
echo "[info ] begin backup project: $warName"

# 备份文件夹
bk_dir=$ITEM_BACKUP/$JOB_NAME

# 创建备份文件夹: 如果文件夹不存在则创建文件夹, 否则删除原来的文件$warName.war
if [ ! -d "$bk_dir" ]; then
  mkdir -p $bk_dir
else
  rm -f $bk_dir/$warName.war
fi

# 备份文件
mv $WORKSPACE/$warName.war $bk_dir
cp $bk_dir/$warName.war $bk_dir/$warName.war.$BUILD_NUMBER

# 记录成功的id
date_time=`date "+%Y%m%d-%H%M"`
echo "$date_time $BUILD_NUMBER $description" >> $ITEM_BACKUP/$JOB_NAME/$ITEM_BID_FILE
```
#### 2.2.6 构建后操作

构建成功之后, 可以进行邮件通知, 将war包上传到ftp, linux 服务器等操作.

