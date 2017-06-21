# Shell 输入输出

> Shell 默认的输入为键盘输入, 默认输出则是终端的Shell 窗口.Shell 脚本中添加合适的输入, 可以让脚本和用户进行交互起来; 合适添加输出语句, 可以让程序运行一目了然.

## 1. 标准输入

shell 脚本中使用 read 命令读取用户输入. read命令会读取一个输入行, 遇到换行符终止.
### 1.1 命令格式
* read \[选项\] \[变量\]  
* read 默认读取一行内容, 遇到回车键结束输入
* 如果不根变量, 默认将读入的内容防止到 REPLY 变量中

### 1.2 常用选项
* -p msg: 显示输入提示:
* -s : 不回显输入内容, 比如输入密码
* -t : 读取超时时间
* -r : 支持读取转义字符, 默认输入\n , \t 等转义字符时,会自动将转义符号 去掉.
* -a 变量名: 读取行按空格分隔, 存入数组中 

### 1.3 示例程序
```bash
#!/bin/bash

echo "please input your info"
read -p "username:" username
read -p "password:" -s password
echo ""
read -p "sex(M/W):" -t 5
read -p "books:" -a books
read -p "brief:" -r brief

echo -e "\nyour input:"
echo -e "username:$username"
echo -e "password:$password"
echo -e "sex:$sex"
echo -e "books:${books[*]}"
echo -e "brief:$brief"
```
** 测试输出:**
```bash
[admin@localhost shell]$ ./read.sh 
please input your info
username:Z & S
password:
sex(M/W):M
books:java mysql
brief:zhang \n \t san

your input:
username:Z & S
password:123456
sex:M
books:java mysql
brief:zhang 
         san
[admin@localhost shell]$ 
```

## 2. 标准输出
shell 脚本中提供了两个用户输出的指令:
* echo: 输出一行字符串
* printf: 格式化输出字符串

### 2.1 echo 指令
echo 指令有三种语法格式:
* echo "" : 输出字符串, 双引号中支持变量, 转义字符
* echo '' : 输出字符串, 是什么就是什么, 变量和转义字符都不会转义
* echo `` : 输出命令执行结果, 反引号内容为命令, 和直接敲命令没什么两样

#### 2.1.1 输出普通字符串
```bash
echo "hello,world!"
```

#### 2.1.2 输出变量
双引号中, 变量引用会自动转换为值, 单引号中不会
```bash
name-zhangsan
echo "name:$name"
echo 'name:$name"
```

#### 2.1.3 开启转义输出
-e 控制是否开启转义字符, 对单引号和双引号都生效
```bash
name="zhangsan"
echo -e "name:\t$name"
echo -e 'name:\t$name'
```

#### 2.1.4 输出原样字符串
输出原样字符需要注意两点:
* 使用单引号: 不转换变量
* 不使用-e选项; 不开启转换转义字符功能
```bash
name="zhangsan"
echo 'name:$name'
```
#### 2.1.5 输出不换行
默认一条echo 语句执行完会添加一个换行符,若想不输出换行符, 可以在开启转义功能的前提下末尾添加\c

```bash
#!/bin/bash

# 输出普通字符串
echo "hello,world!"

# 输出变量
name="zhangsan"
echo "name:\t$name"

# 开启转义输出
echo -e "name:\t$name"

# 输出字符串原样:
echo -e 'name:\t$name'
# 不换行输出
echo -e "hello,\c"
echo "world!"

echo -e 'hello,\c'
echo 'world!'
```

#### 2.1.6 示例程序
```bash


# 不换行输出
echo -e "hello,\c"
echo "world!"

echo -e 'hello,\c'
echo 'world!'
```
**测试输出:**
```bash
[admin@localhost shell]$ ./echo.sh 
hello,world!
name:\tzhangsan
name:   zhangsan
name:   $name
hello,world!
hello,world!
[admin@localhost shell]$ 
```








































