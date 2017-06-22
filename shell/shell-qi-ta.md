# Shell 其它知识

## 1. 程序后台运行

* 语法: 命令 &
* 示例:
  ```bash
  #!/bin/bash
  /opt/app/tomcat/tomcat-8-8080/bin/startup.sh &
  ```

## 2. 程序休眠

* 语法: sleep seconds
* 示例:
  ```bash
  #!/bin/bash
  echo "hello"
  sleep 5
  echo "world"
  ```

## 3. 通配符
通配符: linux 下的通配符用于匹配系统文件名称时, 和正则表达式比较类似, 但不完全相同

### 3.1 常用的通配符


