# Shell 判断表达式

* 格式: \[ 表达式 \]
* 结果: 判断表达是为真返回true, 为假返回false


## 1 数值型判断
虽然说shell 脚本中任何变量都是字符串, 但是比较的表达式时, 却可以区分按数值比较还是按字符串比较. 比如:11 和 2 , 如果按数值比较那么11 > 2, 如果按字符串比较则 11 < 2

**常用判断表达式**

| 表达式 | 判断逻辑 |
| :--- | :--- |
| [ a -eq b ] | 判断相等, 若a==b, 则返回true, 否则返回false |
| [ a -ne b ] | 判断不等, 若a!=b, 则返回true, 否则返回false |
| [ a -gt b ] | 判断大于, 若a>b , 则返回true, 否则返回false |
| [ a -lt b ] | 判断小于, 若a<b , 则返回true, 否则返回false |
| [ a -ge b ] | 判断不小于, 若a>=b,则返回true, 否则返回false |
| [ a -le b ] | 判断不大于, 若a<=b, 则返回true, 否则返回false |


## 2 字符串判断
* 注意在判断为空非空时, 需要将变量用"" 包裹起来

**常用判断表达式**
| 表达式 | 判断逻辑 |
| :--- | :--- |
| [ -z "str" ] | 判断为空, 若为空, 则返回true, 否则返回false |
| [ -n "str" ] | 判断非空,若字符串不为空,则返回true, 否则返回false |
| [ s1 == s2 ] | 判断相等, 若s1和s2相等,则返回true, 否则返回false |
| [ s1 != s2 ] | 判断不相等, 若s1和s2不相等,则返回true, 否则返回false |


## 3 逻辑表达式
shell 中有两种逻辑与和逻辑或表达式, 一种是符号表示 && 和 ||, 另一种是字母表示:-o 和 -a ,但是药注意的用符号表示时, 是两个中括号:[[]], 而用字符表示时是一个中括号[]

### 1. 逻辑
**常用判断表达式**

| 表达式 | 判断逻辑 |
| :--- | :--- |
| [[expr1 && expr2 ]] | 逻辑与, 当表达式1 和表达式2 都为真, 返回true,否则返回false |
| [[ expr1 \|\| expr2 ]] | 逻辑或,当表达式1和表达是2都为假,返回true, 否则返回false |
| [ expr1 -a expr2 ] | 逻辑与, 当表达式1 和表达式2 都为真, 返回true,否则返回false |
| [ expr1 -o expr2 ] | 逻辑或,当表达式1和表达是2都为假,返回true, 否则返回false |
| [ !expr ] | 逻辑非, 当表达式为假返回true, 否则返回false |


## 4 单个文件判断
**常用判断表达式**

| 表达式 | 判断逻辑 |
| :--- | :--- |
| [ -d file ] | 判断文件是否是目录,若是目录,则返回true, 否则返回false |
| [ -e file ] | 判断文件是否存在, 不区分文件类型,若存在,则返回true, 否则返回false |
| [ -f file ] | 判断普通文件是否存在,若存在,则返回true, 否则返回false |
| [ -L file ] | 判断链接文件是否存在,若存在,则返回true, 否则返回false |
| [ -h file ] | 判断硬链接文件是否存在,若存在,则返回true, 否则返回false |
| [ -r file ] | 判断当前用户对文件是否有读权限,若拥有,则返回true, 否则返回false |
| [ -w file ] | 判读当前用户对文件是否有写权限,若拥有,则返回true, 否则返回false |
| [ -x file ] | 判断当前用户对文件是否有执行权限,若拥有,则返回true, 否则返回false |

## 5. 测试程序

```bash
#!/bin/bash

echo "数值判断:"
a=10
b=5

if [ a -eq b ] ; then 
  echo "$a > $b = true"
else
  echo "$a < $b"
fi

echo "字符串判断:"
str=1234

if [ -n "$str" ] ; then
  echo "$str is null"
else
  echo "$str is not null"
fi

echo "文件判断:"
if [ -d /root ] ; then
  echo "directory /root exists"
else
  echo "directory /root not exists"
fi

echo "逻辑判断:"
if [ -d /tmp -o -x /tmp ] ; then
  echo "directory exists and hava x "
fi

if [[ a -ge 10 && a -le 10 ]] ; then
  echo "a=10"
fi
```

** 测试结果:*
```bash
[admin@localhost shell]$ ./test.sh 
数值判断
./test.sh: line 7: [: a: integer expression expected
10 < 5
字符串判断
1234 is null
文件判断:
directory /root exists
联合判断:
directory exists and hava x 
a=10
[admin@localhost shell]$ 
```