# Shell 变量

> Shell 中可使用的变量大致分为四种: 环境变量, 预定义变量, 脚本变量, 用户自定义变量.

## 1. 基础知识

### 1.1 变量分类

* 环境变量: 主要保存的是和系统操作环境相关的变量, 可以新增和修改
* 预定义变量: 系统预设的一些变量
* 脚本变量: 调用脚本时, 传入的参数, 又称位置参数变量
* 用户自定义变量: 脚本中设置的变量

### 1.2 变量名命名

* 变量名和=号直接不能有空格
* 变量名不能以数字开头, 首个字符只能为字母\(A-Z, a-z\)
* 变量名直接不能有空格, 可以使用下划线
* 变量名不能使用标点符号
* 环境变量名建议全部大写

### 1.2 变量赋值

* 变量值与=号直接不能有空格
* 变量类型默认都是字符串类型
* 变量值可以使用特殊符号,转义符号, 如: name="zhang\nsan"
* 变量值可以是命令执行的结果, 用反引号, 如:dt=`date`
* 变量值可以是包含其他变量的表达式, 如:result="hello,$name"

## 2. 环境变量
环境变量通常用于存储一些与系统操作相关的命令, 比如如JAVA_HOME. 在脚本中设置的环境变量, 只在当前脚本中生效, 不会修改系统的环境变量. 通常用于为某些程序的运行准备环境. 比如说, 系统默认安装的是jdk1.7, 而你的程序想要使用jdk 1.8运行 ,那么就可以创建一个脚本, 脚本中先设置JAVA_HOme 的值, 然后再执行你的程序就好了.

### 1. 变量定义
* 语法1: export NAME=value, 定义新的环境变量
* 语法2: export NAME, 将已有用户自定义变量转换为环境变量

### 2. 变量引用
对环境变量的引用有两种语法,语法1比较简单常用, 语法2在特殊场景下使用.如:${NAME}script, 如果用用语法1, 那么会找变量名为 NAMEscript 的值, 而不是NAME值了
* 语法1: $NAME
* 语法2: ${NAME}

### 3. 变量删除:
* 格式: unset NAME

### 4. 查看所有环境变量:
* 命令: env

### 5. 测试脚本:
``` bash
#!/bin/bash

#查看系统环境变量
echo "JAV_HOME:$JAVA_HOME"

# 设置环境变量
export JAVA_HOME=/opt/app/jdk/jdk1.8.0_121
echo "JAVA_HOME:$JAVA_HOME"

# 删除环境变量
unset JAVA_HOME
echo "JAVA_HOME:$JAVA_HOME"
```

** 输出结果: **
脚本中将环境变量JAVA_HOME 删除了, 脚本中便获取不到JAVA_HOME 环境变量了, 但是脚本执行完之后, 直接输出环境变量JAVA_HOME, 会发现依然存在.
``` bash
[admin@localhost shell]$ ./var03.sh 
JAV_HOME:/opt/app/jdk/jdk1.6.0_31
JAVA_HOME:/opt/app/jdk/jdk1.8.0_121
JAVA_HOME:
[admin@localhost shell]$ echo $JAVA_HOME
/opt/app/jdk/jdk1.6.0_31
[admin@localhost shell]$ 
```


## 3. 预定义变量

预定义变量是系统内置的, 用于表示特殊含义的一些变量. 常用的预定义变量有:

| 变量名 | 变量含义 |
| :--- | :--- |
| $? | 记录上次执行的命令返回状态. 返回0 标识执行成功, 否则标识运行错误 |
| $$ | 记录当前的进程的PID |
| $! | 记录上次后台运行的进程的PID |

** 测试脚本 **

```bash
#!/bin/bash
# &> 表示将输出结果重定向, $> /dev/null 表示不输出脚本运行结果

# 系统有此目录, 执行结果成功
ls / &> /dev/null
echo "ls / 执行结果: $?"

# 系统无此目录, 执行结果为失败
ls /hhhh &> /dev/null
echo "ls /hhhh 执行结果: $?"

# 输出当前脚本进程id
echo "当前进程PID: $$"

# 输出上次后台运行进程id
ls / &> /dev/null &
echo "上次后台运行进程id: $!"
```

** 输出结果: **

```bash
[admin@localhost shell]$ ./var01.sh 
ls / 执行结果: 0
ls /hhhh 执行结果: 2
当前进程PID: 22798
上次后台运行进程id: 22853
[admin@localhost shell]$
```

## 4. 脚本变量

### 1. 变量引用
| 变量名 | 变量含义 |
| :--- | :--- |
| $# | 代表传入的参数格式 |
| $* | 代表传入的所有参数, 把所有传入的参数当做是一个字符串变量 |
| $@ | 代表传入的所有参数, 把所有传入的参数当做一个数组 |
| $n | n为数字,从1开始,表示传入的第几个参数,当超过9个参数时,需要用${n} |
| $0 | 命令本身| |

### 2. 示例脚本:
```bash
#!/bin/bash  

echo '参数数量 $#:' $#  
echo '参数列表 $*:' $*  
echo '参数数组 $@:' $@  

echo '调用命令 $0:' $0  
echo '第1 个参数 $1:' $1  
echo '第2 个参数 $2:' $2  
echo '第10个参数 $10:' ${10}  

echo '============== 遍历输出: $*  ========================'  
# 只有一个元素
for i in "$*"
  do
     echo "arg:" $i  
  done

echo '============== 遍历输出: $@  ========================'  
# 有多个元素
for y in "$@"
  do
    echo "arg:" $y  
  done
```

** 测试结果: **
```bash
[admin@localhost shell]$ ./var01.sh 1 2 3 4 5 6 7 8 9 10
参数数量 $#: 10
参数列表 $*: 1 2 3 4 5 6 7 8 9 10
参数数组 $@: 1 2 3 4 5 6 7 8 9 10
调用命令 $0: ./var01.sh
第1 个参数 $1: 1
第2 个参数 $2: 2
第10个参数 $10: 10
============== 遍历输出: $*  ========================
arg: 1 2 3 4 5 6 7 8 9 10
============== 遍历输出: $@  ========================
arg: 1
arg: 2
arg: 3
arg: 4
arg: 5
arg: 6
arg: 7
arg: 8
arg: 9
arg: 10
[admin@localhost shell]$ 
```

## 5. 用户之定义变量

用户自定义的变量,作用域为当前脚本, 也就是说只能在脚本内使用.

### 1. 变量定义:

* 格式: name=value
* 注意: =号两边不能有空格

### 2. 变量引用:

对环境变量的引用有两种语法,语法1比较简单常用, 语法2在特殊场景下使用如: ${name}script, 如果用用语法1, 那么会找变量名为 namescript 的值, 而不是name 值了

* 语法1: $name
* 语法2: ${name}

### 3. 变量删除:

* 格式: unset name

### 4. 测试脚本:

```bash
#!/bin/bash

#定义变量
name="java"

#变量引用
echo "name:$name"
echo "book:${name}Script"

#删除变量
unset name
echo "name:$name"
```

** 测试输出: **
```bash
\[admin@localhost shell\]$ ./var02.sh   
name:java  
book:javaScript  
name:  
\[admin@localhost shell\]$
```
