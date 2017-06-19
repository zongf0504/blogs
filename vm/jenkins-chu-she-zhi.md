# jenkins 基本设置

## 1. jenkins 系统管理

点击jenkins-&gt; 系统管理, 右侧会列出jenkins 相关管理功能  
![](/assets/jenkins_2017-06-15_191110.png)

常用功能:

* 系统配置: 配置全局路径, 全局属性, 邮件服务器, ssh, ftp服务器等.
* Configure Global Security: 配置用户数据库, 授权策略等
* Global Tool Configuration: 配置全局工具, jdk, git, maven 等
* 读取配置: 重新读取配置, 会重新jenkins服务
* 管理插件: 对插件的添加, 删除, 更新等操作
* 系统信息: jenkins 所在服务器的系统信息
* System Log: jenkins 服务输出日志
* 管理用户: jenkins 用户管理

## 2. 系统设置

### 2.1 设置工作目录

打开系统设置: jenkins-&gt;系统管理-&gt;系统设置, 修改如图  
![](/assets/jenkins_2017-06-15_193019.png)

* 主目录: 此目录不能修改, 为启动jenkins服务时, 在tomcat 启动脚本中设置的JENKINS\_HOME路径, 默认地址为家目录的.jenkins 目录
* 工作空间根目录: 此目录用于存放每个job从git/svn 上下载的源代码.
* 构建记录根目录:  此目录用于存放每个job的构建记录.

其中${ITEM\_FULLNAME}为jenkins内置变量, 此变量为新建job 时job 的名称,

### 2.2 设置全局变量属性

在自动化部署项目时, 应该定期对部署成功的项目做备份, 这样既可以记录部署成功的次数,又能当新版本出现问题时及时回滚.但是jenkins 并未提供备份相关的内置变量,所以需要手工设置全局变量.全局变量在构建任务配置中任何地方都可以使用, 比如说svn地址中, 脚本中... 此处我们先设置两个备份相关的全局变量:

* ITEM\_BACKUP: 备份文件的根文件夹
* ITEM\_BID\_FILE: 备份成功的记录信息文件
  ![](/assets/jenkins_2017-06-17_064301.png)

### 2.3 publish over SSH

当部署的应用服务器\(如tomcat\) 和jenkins 并不在同一台Linux 服务器上时, 称为远程Linux服务器部署. 此种情况需要借助于Publish over ssh 插件, 在本地准备好jar包, 通过Publish over ssh 插件将war包发送到远程服务器, 并通过此插件在远程服务器上执行重部署脚本. 远程服务器需要在系统设置中配置ip 地址, 用户名, 密码, 默认目录等基本信息.  
![](/assets/jenkins_2017-06-17_064204.png)

## 3. 全局工具设置

### 3.1 设置jdk 环境

点击 jenkins -&gt; 系统管理 -&gt; Global Tool Configuration:  
点击新增JDK, 输入别名和JAVA\_HOME 目录, 前提是你服务器已经安装了jdk环境, 不要勾选自动安装, 用服务器上已经安装好的jdk 即可. 笔者服务器上安装了两个版本的jdk, 所以配置了两个, 这个为默认选中的jdk. ** 不要勾选自动安装** 
![](/assets/jenkins_20170619150455.png)

### 3.2 设置maven 环境

点击 jenkins -&gt; 系统管理 -&gt; Global Tool Configuration:  
点击 新增Maven, 设置Name 和 MAVEN\_HOME 变量\(Maven 安装目录\), 此处需要注意, 通常maven 需要做基本的设置, 比如本地工厂位置, nexus 私服镜像等, 这个修改maven 的settings 文件即可.  ** 不要勾选自动安装** 

![](/assets/jenkins_2017-06-19_150704.png)

### 3.3 设置git 环境

点击 jenkins -&gt; 系统管理 -&gt; Global Tool Configuration:  
点击 新增Git, 输入本地安装的git 命令位置, 可在本地使用 which git 命令查看.  ** 不要勾选自动安装** 

![](/assets/jenkins_2017-06-15_195249.png)

## 4. 全局安全设置

点击 jenkins -&gt; 系统管理 -&gt; Configure Global Security:  
![](/assets/jenkins_2017-06-15_195937.png)

* 安全域: 用户数据库选择jenkins专用数据库, 用户由jenkins维护
* 授权策略; 选择登录用户可以做任何事,这样jenkins就必须登录之后才能使用
  对于其它模式的配置, 会在权限控制文章中详细讲解



