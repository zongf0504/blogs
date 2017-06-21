# Shell 流程控制-[判断分支
> Shell 语言中用于判断的分支的语句有if-else 和 case. 


## 1. if-else
* if-else 的判断表达式为中括号[]
* 注意关键字elif, 不是elseif, 不要搞错了
* 注意是以关键字fi 结尾, 不是以if 结尾
* 注意if , [, 条件表达式, ] , ; , then 直接都要有空格
* 注意代码缩进

### 1. if-fi 结构
* shell 中的if 语句有两种写法, 笔者习惯于使用第一种.
* 含义: 如果表达式为true, 则执行程序

```bash
# 第一种写法: 需要写; 要注意缩进  
if [ 条件判断表达式 ] ; then  
    # 程序块儿  
fi  
      
# 第二中写法: 不需要写; 要注意缩进  
if [ 条件判断表达式 ]  
  then  
     #程序块儿  
fi  
```

### 2. if-else-fi 结构
* 由于if 有两种写法, 所以对应的if-else-fi 也有两种写法, 笔者就只列出第一种结构了
* 含义: 如果条件表达式为true,则执行程序块儿一, 否则执行程序块儿二

```bash
if [ 条件判断表达式 ] ; then  
    # 程序块儿一  
else   
    # 程序块儿二  
fi  
```

### 3. if-elif=else-fi
* 由于if 有两种写法, 所以对应的if-elif-else-fi 也有两种写法, 笔者就只列出第一种结构了
* 含义: 如果条件表达式一为true,则执行程序块儿一,如果条件表达式二为true,则执行代码块儿二, 否则执行程序块儿三

```bash
if [ 条件判断表达式 ] ; then  
    # 程序块儿一  
elif [ 条件判断表达式二 ] ; then
    # 程序块儿二
else   
    # 程序块儿三  
fi  

```

### 2.4 示例程序

```bash
#!/bin/bash  
  
read -p "请输入一个数字:" num  
  
if [ $num -eq 20 ] ; then  
  echo "$num > 20"  
elif [ $num -eq 100 ] ; then  
  echo "$num > 100"  
else  
  echo "$num < 20"  
fi  
```

** 输出结果 **
```bash
[admin@localhost shell]$ ./if.sh 
请输入一个数字:1
1 < 20
[admin@localhost shell]$ 
```

## case
case 是多分枝选择结构, 即一个判断表达式可以有多种匹配选项.
* ;; 为跳出case 语句,类似于java 中的break;
* * 表示什么都不匹配时执行.

### 1. case 结构
```bash
case 字符串表达式 in  
  "值1")  
    程序块儿  
    ;; #跳出case结构,相当于break;  
  "值2")  
    程序块儿  
    ;;  
   ...  
  *)  
    程序块儿 (不满足以上所有条件)  
    ;;  
esac  
```

### 2. 示例程序

```bash
#!/bin/bash  

#输出菜单
echo "        Menu    "  
echo "  1. Beijing - Tianjin"  
echo "  2. Tianjin - Beijing"  
echo "  3. qingdao - Beijing"  
echo "  4. Beijing - Qingdao"  
  
#读入输出:
read -p "Please input your chooise: " jour  
 
#判断用户输入
case "$jour" in  
    "1")  
        echo " Beijing - Tianjin "  
        ;;  
    "2")  
        echo " Tianjin - Beijing "  
        ;;  
    "3")  
        echo " Qingdao - Beijing "  
        ;;  
    "4")  
        echo " Beijing - Qingdao "  
        ;;  
    *)  
        echo " Your chooise Error ！"  
        ;;  
esac 
```

** 输出结果: **
```bash
[admin@localhost shell]$ ./case.sh 
        Menu    
  1. Beijing - Tianjin
  2. Tianjin - Beijing
  3. qingdao - Beijing
  4. Beijing - Qingdao
Please input your chooise: 1
 Beijing - Tianjin 
[admin@localhost shell]$ 
```
## 判断表达式























