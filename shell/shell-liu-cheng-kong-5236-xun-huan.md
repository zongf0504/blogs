# Shell 流程控制-循环

> Shell 脚本中的循环有三种方式, for, while, until. 其中until 和 while 表示的含义正好相反, while 标识当判断表达式为真时, 则执行循环语句, until 一直循环到判断表达式为真, 即当判断表达式为假时执行.这三种循环可以相互转化的, 所以熟练掌握一种即可. 笔者平时使用for 和 while 居多, 很少使用until.

## 1. for 循环

### 1.1 语法

```bash
#语法一  
for 控制变量 in 值1 值2 值3..  
   do  
     程序块儿  
   done  

#语法二
for 控制变量 in (起始值..终止值)
  do
    程序块儿
  done


#语法三  
for 控制变量 `命令`  
   do  
     程序块儿  
   done  


#语法四  
for ((初始值; 循环控制; 变量变化))  
  do  
    程序块儿  
  done
```

### 1.2 示例程序

```bash
#!/bin/bash

echo "[语法一] 输出五次系统时间:"
for i in 1 2 3 4 5
  do
    echo "$i-->$(date)";
  done

echo "[语法二] 输出五次系统时间:"
for i in {1..5}
  do
    echo "$i-->$(date)";
  done


echo -e "\n[语法三] 遍历根目录下所有文件:"
for item in `ls -d /s*`
  do
    echo "file: $item"
  done

echo -e "\n[语法四] 求和:"
#注意变量赋值的时候,=两边绝对不能有空格  
sum=0
for (( i=1; i<=100; i++ ))
  do
   sum=$(( $sum + $i ))
  done

echo "1+2+3+...+100=$sum" 
~
```

**输出结果**

```bash
[admin@localhost shell]$ ./for.sh 
[语法一] 输出五次系统时间:
1-->Wed Jun 21 17:18:34 CST 2017
2-->Wed Jun 21 17:18:34 CST 2017
3-->Wed Jun 21 17:18:34 CST 2017
4-->Wed Jun 21 17:18:34 CST 2017
5-->Wed Jun 21 17:18:34 CST 2017
[语法二] 输出五次系统时间:
1-->Wed Jun 21 17:18:34 CST 2017
2-->Wed Jun 21 17:18:34 CST 2017
3-->Wed Jun 21 17:18:34 CST 2017
4-->Wed Jun 21 17:18:34 CST 2017
5-->Wed Jun 21 17:18:34 CST 2017

[语法三] 遍历根目录下所有文件:
file: /sbin
file: /selinux
file: /srv
file: /sys

[语法四] 求和:
1+2+3+...+100=5050
```

## 2. while 循环

while 循环中可以使用break 和 continue进行跳出循环, 和Java 用法相同;

* continue: 跳出本次循环, 执行下一次循环
* break: 跳出整个while 循环

### 2.1 while 结构

```bash
while [ 条件判断式 ]  
  do  
    程序块儿  
    break/continue  
  done
```

### 2.2 示例程序

```bash
#!/bin/bash  

#计算1+2+3+...+100的和
i=1  
sum=0  
while [ $i -le 100 ]  
  do  
    sum=$(( $sum + $i ))  
    i=$(( $i + 1 ))  
  done  
echo "1+2+3+4+...+100=$sum"
```

**输出结果:**

```bash
[admin@localhost shell]$ ./while.sh 
1+2+3+4+...+100=5050
[admin@localhost shell]$
```

## 3. util 循环
util 表示一直循环执行到判断表达式为真时停止, 即当判断表达式为假时执行, 为真时停止执行.用until能实现的循环用while 和for 都能实现, 只是until 或许可读性更好一点儿而已.

### 3.1 语法结构

```bash
until [ 条件判断式 ]  
  do  
    程序块儿  
  done  
```

### 3.2 示例程序

```bash
#!/bin/bash  

i=1  
sum=0  
until [ $i -gt 100 ]  
  do  
	sum=$(( $sum + $i ))  
	i=$(( $i + 1 ))   
  done  
echo "1+2+3+4+...+100=$sum"  
```

** 测试输出: **
```bash
[admin@localhost shell]$ ./until.sh 
1+2+3+4+...+100=5050
[admin@localhost shell]$ 
```







