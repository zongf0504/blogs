# 安装Apache tomcat 服务器

> Apache tomcat 是一款优秀的j轻量级Java 应用服务器, 用来运行java 项目. 在中小型系统和并发访问用户不是很多的场景下被广泛使用, 尤其是开发测试Javaweb 应用的首选服务器. 其实apache 并不是一个软件, 只是用java 语言写的一个应用而已, 所以,tomcat 的运行需要依赖jdk.

## 1. 安装tomcat
### 1.1 下载安装包

Apache 官网下载地址: [http://tomcat.apache.org/](http://tomcat.apache.org/)
笔者同时安装tomcat7 和 tomcat8
* apache-tomcat-7.0.78.tar.gz
* apache-tomcat-8.0.44.tar.gz

### 1.2 上传安装包

tomcat 安装到 /opt/app/tomcat 目录下, 备份文件存储到 /opt/source 目录下, 若目录不存在,自行创建.
使用** WinCP **工具将tomcat 压缩包上传到 /opt/app/tomcat 目录下, 使用admin 用户登录WinCP

### 1.3 解压

使用admin 用户登录linux 系统, 进入目录 /opt/app/tomcat
```bash

[admin@localhost ~]$ cd /opt/app/tomcat
[admin@localhost tomcat]$ tar -zxf apache-tomcat-7.0.78.tar.gz
[admin@localhost tomcat]$ tar -zxf apache-tomcat-8.0.44.tar.gz
[admin@localhost tomcat]$ ll
total 8
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-7-7080
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-8-8080

```

### 1.4 备份

``` bash

mv /opt/app/tomcat/*.tar.gz /opt/source

```

### 1.5 重命名
解压后的tomcat 最好按一定的规则命名, 这样便于管理.笔者通常会采用格式: ** tomcat-版本号-端口号[-应用简称]** 来命名, **端口号设定规则, 第一位代表tomcat 版本号**, 比如说: tomcat-8-8080-jenkins, 由于此时我们并不需要部署什么应用, 所以我们命名为 tomcat-7-7080, tomcat-8-8080
``` bash
[admin@localhost tomcat]$ mv tomcat-7-7080/ tomcat-7-7080/
[admin@localhost tomcat]$ mv tomcat-7-7080/ tomcat-7-7080/
[admin@localhost tomcat]$ ll
total 8
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-7-7080
drwxrwxr-x. 9 admin admin 4096 May 21 06:28 tomcat-8-8080
[admin@localhost tomcat]$ 

```

## 2. tomcat 目录讲解
* **bin** : tomcat 的启动关闭等脚本
* **conf** : tomcat 相关配置文件
* **lib** : tomcat 依赖的jar包(tomcat 本身就一java 应用, 也需要依赖一些第三方jar包)
* **logs** : tomcat 日志文件, 三种类型日志: catalina, localhost, host-manager, 按天自动切割, 即每天产生一个新文件
* **temp** : 临时目录
* **webapps** : 应用部署目录,将war包丢进此目录即可完成部署, 会自动将war 目录解压
* **work/Catalina/localhost** : 应用工作目录,每部署一个应用, 此目录会产生一个与应用同名称的文件夹, 因此重新部署时,通常建议删除此目录中相应的文件夹,以防止新部署应用不生效.
* **...** : 一些说明文件

## 3. tomcat 启动 & 停止
网上很多人会建议配置CATALINA_HOME 环境变量, 笔者认为并没有此必要, 使用绝对路径起停服务就好了, 毕竟tomcat 只是一个java 的应用而已, 况且通常情况下, 一台服务器上肯定不止部署一个tomcat.

### 3.1 启动tomcat
tomcat 自带启动脚本, 为bin 目录下的 startup.sh. 我们来启动tomcat-8-8080, 笔者启动前,喜欢清空一下tomcat 的输出日志, 方便查看本次启动应用的情况.

**清空日志**
``` bash

[admin@localhost tomcat]$ echo "" > ./tomcat-8-8080/logs/catalina.out

```

**启动tomcat**
``` bash

[admin@localhost tomcat]$ /opt/app/tomcat/tomcat-8-8080/bin/startup.sh                          
Using CATALINA_BASE:   /opt/app/tomcat/tomcat-8-8080
Using CATALINA_HOME:   /opt/app/tomcat/tomcat-8-8080
Using CATALINA_TMPDIR: /opt/app/tomcat/tomcat-8-8080/temp
Using JRE_HOME:        /opt/app/jdk/jdk1.7.0_80
Using CLASSPATH:       /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar
Tomcat started.
[admin@localhost tomcat]$

```

### 3.2 查看进程
通过ps 命令查看进程, 会发现虽然我们使用的 startup.sh 启动的, 单实则
```

[admin@localhost tomcat]$ ps -ef | grep -v grep | grep tomcat-8-8080
admin      2739      1  3 00:42 pts/1    00:00:03 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-8-8080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-8-8080/endorsed -classpath /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-8-8080 -Dcatalina.home=/opt/app/tomcat/tomcat-8-8080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-8-8080/temp org.apache.catalina.startup.Bootstrap start
[admin@localhost tomcat]$ 

```

### 3.3 访问页面

### 3.4 关闭tomcat 服务器
``` bash

[admin@localhost tomcat]$ /opt/app/tomcat/tomcat-8-8080/bin/shutdown.sh 
Using CATALINA_BASE:   /opt/app/tomcat/tomcat-8-8080
Using CATALINA_HOME:   /opt/app/tomcat/tomcat-8-8080
Using CATALINA_TMPDIR: /opt/app/tomcat/tomcat-8-8080/temp
Using JRE_HOME:        /opt/app/jdk/jdk1.7.0_80
Using CLASSPATH:       /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar
[admin@localhost tomcat]$ 

```

## 4. 修改tomcat 配置文件

### 1. 修改端口号
linux 操作系统, 一个端口号只能被一个实例所占用, 而tomcat 默认使用端口号8080, 当一台服务器上需要启动多个tomcat 实例 时, 那么就需要修改tomcat 的占用的端口号了, 只需要将tomcat 默认的**8005, 8080, 8009** 三个端口修改就行了. 笔者来修改 tomcat-7-7080 的端口, 修改为 **7005, 7080, 7009**

``` bash

[admin@localhost tomcat]$ vim /opt/app/tomcat/tomcat-7-7080/conf/server.xml 

```
配置文件内容: server.xml
***
``` xml
<?xml version='1.0' encoding='utf-8'?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<!-- Note:  A "Server" is not itself a "Container", so you may not
     define subcomponents such as "Valves" at this level.
     Documentation at /docs/config/server.html
 -->
<Server port="7005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <!-- Security listener. Documentation at /docs/config/listeners.html
  <Listener className="org.apache.catalina.security.SecurityListener" />
  -->
  <!--APR library loader. Documentation at /docs/apr.html -->
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <!--Initialize Jasper prior to webapps are loaded. Documentation at /docs/jasper-howto.html -->
  <Listener className="org.apache.catalina.core.JasperListener" />
  <!-- Prevent memory leaks due to use of particular java/javax APIs-->
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <!-- Global JNDI resources
       Documentation at /docs/jndi-resources-howto.html
  -->
  <GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <!-- A "Service" is a collection of one or more "Connectors" that share
       a single "Container" Note:  A "Service" is not itself a "Container",
       so you may not define subcomponents such as "Valves" at this level.
       Documentation at /docs/config/service.html
   -->
  <Service name="Catalina">

    <!--The connectors can use a shared executor, you can define one or more named thread pools-->
    <!--
    <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="150" minSpareThreads="4"/>
    -->


    <!-- A "Connector" represents an endpoint by which requests are received
         and responses are returned. Documentation at :
         Java HTTP Connector: /docs/config/http.html (blocking & non-blocking)
         Java AJP  Connector: /docs/config/ajp.html
         APR (HTTP/AJP) Connector: /docs/apr.html
         Define a non-SSL HTTP/1.1 Connector on port 8080
    -->
    <Connector port="7080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    <!-- A "Connector" using the shared thread pool-->
    <!--
    <Connector executor="tomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    -->
    <!-- Define a SSL HTTP/1.1 Connector on port 8443
         This connector uses the BIO implementation that requires the JSSE
         style configuration. When using the APR/native implementation, the
         OpenSSL style configuration is required as described in the APR/native
         documentation -->
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
               clientAuth="false" sslProtocol="TLS" />
    -->

    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector port="7009" protocol="AJP/1.3" redirectPort="8443" />


    <!-- An Engine represents the entry point (within Catalina) that processes
         every request.  The Engine implementation for Tomcat stand alone
         analyzes the HTTP headers included with the request, and passes them
         on to the appropriate Host (virtual host).
         Documentation at /docs/config/engine.html -->

    <!-- You should set jvmRoute to support load-balancing via AJP ie :
    <Engine name="Catalina" defaultHost="localhost" jvmRoute="jvm1">
    -->
    <Engine name="Catalina" defaultHost="localhost">

      <!--For clustering, please take a look at documentation at:
          /docs/cluster-howto.html  (simple how to)
          /docs/config/cluster.html (reference documentation) -->
      <!--
      <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>
      -->

      <!-- Use the LockOutRealm to prevent attempts to guess user passwords
           via a brute-force attack -->
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <!-- This Realm uses the UserDatabase configured in the global JNDI
             resources under the key "UserDatabase".  Any edits
             that are performed against this UserDatabase are immediately
             available for use by the Realm.  -->
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
    </Engine>
  </Service>
</Server>
```
***

### 2. 修改get请求编码
tomcat 8之前的默认编码为iso-8859-1, 因此tomcat 在处理get 请求时中文会出现乱码的情况,  通常在应用中需要对此情况做特殊处理,或者修改服务器默认编码为utf8, 修改方式.

``` bash

[admin@localhost tomcat]$ vim /opt/app/tomcat/tomcat-7-7080/conf/server.xml 

```

配置端口7080 的xml 片段后面添加 URIEncodeing="UTF-8"

***
``` xml
<Connector port="7080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8" />

```
***


## 5. 修改tomcat 启动文件

### 1. 修改JDK 路径
当服务器上安装的jdk 版本并非你想用的jdk 


#set java home
export JAVA_HOME=/home/zonggf/jdk/jdk1.8.0_121


### 2. 修改JVM 内存
JAVA_OPTS="-Xms516m -Xmx1024m -Xss1024K -XX:PermSize=512m -XX:MaxPermSize=512m"












