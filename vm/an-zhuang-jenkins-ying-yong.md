# jenkins

> jenkins 是一款基于Java 开发的持续性集成工具,用于监控和执行持续性地重复工作.

## 1. 安装

### 1.1 安装说明

* jenkins 针对不同系统提供了不同的安装包, 笔者下载的是通用的jenkins.war, 版本号: 2.46.3
* jenkins 依赖java 环境, 所以必须安装jdk环境, jenkins 2.46.3 需要jdk 1.8 以上
* jenkins.war 需要部署在web容器中, 笔者采用的是tomcat 8
* 下载地址: [https://jenkins.io/download/](https://jenkins.io/download/)

目录结构:

* jdk1.8 : /opt/app/jdk/jdk1.8.0\_131
* tomcat8 : /opt/app/jenkins/tomcat-8081-jenkins
* jenkins.war : /opt/app/jenkins/tomcat-8081-jenkins/webapps
* jenkins data: /var/data/.jenkins/jenkins

### 1.2 安装

#### 1.2.1 安装jdk 1.8 和 tomcat8 到指定目录

* 安装 jdk1.8
* 安装 tomcat 8, 重命名为 tomcat-8081-jenkins
* 将jenkins.war 拷贝到${tomcat\_home}/webapps 目录下

#### 1.2.2 修改tomcat 端口号

编辑配置文件 ${tomcat\_home}/conf/server.xml , 修改一下端口号

| 原端口号 | 新端口号 |
| :---: | :---: |
| 8009 | 8109 |
| 8080 | 8081 |
| 8005 | 8105 |

#### 1.2.3 修改启动脚本

* jenkins 默认会将所有数据存放在~/.jenkins 目录中, 这样并不是最佳方案,而且容易产生多种问题, 所以我们需要在启动tomcat 时指定一下JENKINS_HOME 目录
* 启动tomcat 默认占用256 M内存, 如果编译项目过大的话, 可能不够, 可以修改参数调整
* 启动tomcat 默认使用环境变量中的jdk, 可以手工设置指定的jdk 
  编辑配置文件: ${tomcat_home}/bin/catalina.sh , 在文件非注释行前添加:

```bash
export JAVA_HOME=/opt/app/jdk/jdk1.8.0_131  
export JENKINS_HOME=/var/data/jenkins  
JAVA_OPTS="-Xms256m -Xmx1024m -Xss1024K -XX:PermSize=256m -XX:MaxPermSize=256m"
```

#### 1.2.4 启动tomcat

```bash
/opt/app/jenkins/tomcat-8081-jenkins/bin/startup.sh
```

#### 1.2.5 查看jenkins 默认密码

```bash
[admin@localhost ~]$ cat /var/data/.jenkins/jenkins/secrets/initialAdminPassword
17f4c544ab1f4b419b048446a09d5603
```

#### 1.2.6 访问jenkins

1. 确保防火墙已关闭, 或端口号8081 已释放
2. 使用火狐浏览器访问: [http://192.168.145.100:8081/jenkins](http://192.168.145.100:8081/jenkins)

#### 1.2.7 初始化设置

1. ** 输入默认的密码, 密码记录在图片中位置 **  
 ![](/assets/jenkins_2017-06-19_144426.png)

2. ** 选择跳过安装默认插件步骤 **  
   ![](/assets/jenkins_2017-06-06_170031.png)

3. ** 创建一个管理员用户 **  
   ![](/assets/jenkins_2017-06-19_135705.png)

4. ** jenkins 主页 **  
   ![](/assets/jenkins_2017-06-06_170430.png)
   
能看到此页面, 那么恭喜你, 你的jenkins已经起来了. 


