# Shell 数组变量
> shell 脚本是支持数组变量的, 但仅支持一维数组, 不支持多维数组.


## 1. 数组定义
* 数组元素均为字符串类型
* 数组元素直接用空格隔开, 如果元素值本身包含空格,则用引号包裹元素
* = 号左右两边不能有空格

### 1.1 普通定义
* 语法: name=(value1 ... value)
* 示例:
```bash
 books=( JAVA "JAVA SCRIPT" )
```

### 1.2 赋值法定义
* 语法: name[0]=value0
* 示例:
```bash
books[0]=JAVA
books[1]="java script"
```

## 2. 数组引用
### 2.1 单个元素引用
* 语法: ${name[index]}
* 数组下标从0 开始
```bash
echo "$books[0]"
```

### 2.2 数组引用
@ 和 * 都代表全部元素
* 语法1: ${name[@]}
* 语法2: ${name[*]}
* 示例:
``` bash
echo "${books[*]}"
echo "${books[@]}"
```

### 2.3 数组长度引用
* 语法: ${#name[0]}
* 语法: ${#name[1]}
* 示例:
```bash
echo "length:${#name[*]}"
echo "length:${#name[@]}"
```

## 3. 数组删除
### 3.1 删除数组元素
* 语法: unset name[index]
* 示例:
```bash
unset books[0]
```

### 3.2 删除数组
* 语法: unset name
* 示例:
```bash
unset books
```

## 4. 遍历数组
### 4.1 遍历元素:
此种变量简单,但是不能获取元素索引值
```bash
for item in ${name[*]}
do 
  echo $item
done
```

### 4.2 按序号遍历
此种变量稍微麻烦, 但是能获取到元素索引
```bash
for (( i=0; i<${name[*]}; i++ )) 
do
   echo "item$i: ${name[$i]}
done
```

## 5. 用法示例:
```bash
#!/bin/bash

#定义数组:
books=( "JAVA SCRIPT" LINUX MYSQL )

#定义数组
arrays[0]=1
arrays[1]=2
arrays[3]="hello"

echo "数组元素引用:"
echo "arrays[0]: ${arrays[0]}"
echo "books[0]: ${books[0]}"

echo "数组引用:"
echo "arrays: ${arrays[*]}"
echo "books: ${books[@]}"

echo "数组长度:"
echo "arrays length: ${#books[*]}"
echo "books length: ${#books[@]}"

echo "遍历数组arrays:"
for item in ${arrays[*]}
do
   echo $item
done

echo "遍历数组books:"
for ((i=0; i<${#books[*]}; i++ ))
do
  echo "第$i个元素: ${books[$i]}"
done

unset books[0]
echo "删除第一个元素后,books:${books[*]}"

unset books
echo "删除books 数组后, books:$books"

```

** 测试输出: **
```bash
[admin@localhost shell]$ ./array.sh 
数组元素引用:
arrays[0]: 1
books[0]: JAVA SCRIPT
数组引用:
arrays: 1 2 hello
books: JAVA SCRIPT LINUX MYSQL
数组长度:
arrays length: 3
books length: 3
遍历数组arrays:
1
2
hello
遍历数组books:
第0个元素: JAVA SCRIPT
第1个元素: LINUX
第2个元素: MYSQL
删除第一个元素后,books:LINUX MYSQL
删除books 数组后, books:
[admin@localhost shell]$ 
```