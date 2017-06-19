# jenkins 常用插件

> jenkins 的一个优点就是拥有大量的,丰富的开源插件可以使用, 依据插件可以很方便地实现很多功能.笔者是做java 开发的, 所以安装的都是java 必须的插件.

## 1 常用插件

| 插件名称 | 插件描述 |
| :--- | :--- |
| subverson plugin | 源码管理 |
| git plugin | 源码管理 |
| maven integration plugin | maven 编译 |
| Publish Over SSH | sh协议发布 |
| Publish Over FTP | ftp协议发布 |
| Cron Column Plugin | 定时任务列 |
| Description Column Plugin | 描述列 |
| Build Monitor View | dashboard 监控 |
| Job Configuration History Plugin | 配置历史监控 |
| Role-based Authorization Strategy | 角色管理 |
| SSH2 Easy Plugin | ftp download |
| build timeout plugin  | 任务执行超时插件 |
| JDK Parameter Plugin | 参数化jdk,可选择jdk |

## 2 插件安装
jenkins插件有两种安装方式, 一种是在线安装, 一种是离线安装, 如果能联网的话,笔者还是推荐使用在线安装方式. 因为安装一个插件时,可能这个插件又依赖了其它的插件,若是一个一个安装,必然比较麻烦,而在线安装却能很好地解决这个问题,选择一个插件安装时,会将此插件依赖的相关插件都安装了.

安装插件的方式比较类似, 笔者用jdk parameter plugin 举例:

### 2.1 在线安装插件
#### 2.1.1 打开插件管理
依次点击左上角 jenkins -> 系统管理 -> 管理插件
![](/assets/jenkins_2017-06-15_181449.png)

#### 2.1.2 选择要安装的插件
点击可选插件 -> 过滤框输入jdk -> 勾选JDK PArameter Plugin -> 点击直接安装
* 可更新: 已安装的插件,有新版本可供更新
* 可选插件: 未安装的插件, 可选择安装
* 已安装: 已经安装的插件
* 高级: 手工安装插件和设置网路代理 
![](/assets/jenkins_2017-06-07_095959.png)

#### 2.1.3 安装进度
点击直接安装之后, 会跳转到安装进度列表, 会罗列出出此插件依赖的所有插件,并以此安装
![](/assets/jenkins_2017-06-07_100126.png)

### 2.2 离线安装插件
对于不能联网的服务器, 我们可以采用离线的方式安装插件, 离线安装是比较麻烦的, 因为插件的jpi 文件比较多, 又不能批量上传, 所以只能一个一个地选择, 而且插件具有依赖性, 得按顺序安装才行, 否则是安装不成功的. 笔者的安装顺序是:

``` bash
antisamy-markup-formatter
bouncycastle-api
build-monitor-plugin
credentials
structs
cron_column
description-column-plugin
display-url-api
external-monitor-job
windows-slaves
scm-api
workflow-step-api
workflow-scm-step
token-macro
ssh-credentials
ssh
JDK_Parameter_Plugin
junit
mapdb-api
icon-shim
matrix-auth
script-security
matrix-project
javadoc
mailer
subversion
maven-plugin
pam-auth
git-client
git
ant
build-timeout
jobConfigHistory
ldap
ssh2easy
publish-over-ftp
publish-over-ssh
role-strategy
```

#### 2.2.1 下载插件
jenkins 的插件可以从互联网上下载

#### 2.2.2 上传插件
点击 jenkins -> 系统管理 -> 插件管理 -> 高级 -> 上传插件
![](/assets/jenkins_2017-06-19_143704.png)

#### 2.3.3 重启jenkins
点击jenkins -> 系统管理 -> 读取配置

#### 2.3.4 查看
点击 jenkins -> 系统管理 -> 插件管理 -> 已安装
![](/assets/jenkins_2017-06-19_143838.png)





















































