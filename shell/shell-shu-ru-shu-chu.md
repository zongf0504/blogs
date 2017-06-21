# Shell 输入输出

> Shell 默认的输入为键盘输入, 默认输出则是终端的Shell 窗口.Shell 脚本中添加合适的输入输出语句, 可以让脚本与人交互起来.

## 1. 标准输入

shell 脚本中使用 read 命令读取用户输入. read命令会读取一个输入行, 遇到换行符终止.
### 1.1 命令格式
* read \[选项\] \[变量\]  

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
