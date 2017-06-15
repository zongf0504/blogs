# jenkins 常用插件

> jenkins 的一个优点就是拥有大量的,丰富的开源插件可以使用, 依据插件可以很方便地实现很多功能.

## 1.1 常用插件

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

## 1.2 插件安装示例
jenkins插件有两种安装方式, 一种是在线安装, 一种是离线安装, 如果能联网的话,笔者还是推荐使用在线安装方式. 因为安装一个插件时,可能这个插件又依赖了其它的插件,若是一个一个安装,必然比较麻烦,而在线安装却能很好地解决这个问题,选择一个插件安装时,会将此插件依赖的相关插件都安装了.

安装插件的方式比较类似, 笔者用jdk parameter plugin 举例:

### 1.2.1 打开插件管理
依次点击左上角 jenkins -> 系统管理 -> 管理插件
![](/assets/jenkins_2017-06-15_181449.png)

### 1.2.2 选择要安装的插件
点击可选插件 -> 过滤框输入jdk -> 勾选JDK PArameter Plugin -> 点击直接安装
* 可更新: 已安装的插件,有新版本可供更新
* 可选插件: 未安装的插件, 可选择安装
* 已安装: 已经安装的插件
* 高级: 手工安装插件和设置网路代理 
![](/assets/jenkins_2017-06-07_095959.png)

#### 1.2.3 安装进度
点击直接安装之后, 会跳转到安装进度列表, 会罗列出出此插件依赖的所有插件,并以此安装
![](/assets/jenkins_2017-06-07_100126.png)
























































