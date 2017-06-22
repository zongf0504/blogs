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