# Shell 输入输出重定向

> Shell 默认的输入为键盘输入, 默认输出则是终端的Shell 窗口. 但是有时候我们希望某些命令输出的内容不显示,或者想将输出的结果写入其它文件, 这就用到了输出重定向. 输出重定向不常用, 所以就不做描述了.

## 1. Linux 下的标准输入输出

Linux 下一切皆文件, 输入输出也不例外.虽然标准输入时键盘输入, 标准输出为终端显示器,但是在linux 中标准输入输出也对应着响应的文件. 一般情况下, 每个Linux 命令运行时都会打开三个文件:

* 标准输入文文件\(/dev/stdin\): 文件描述符为0, linux 程序默认从stdin 读取数据
* 标准正确输出文件\(/dev/stdout\): 文件描述符为1, linux 程序默认将正确输出写入stdout中
* 标准错误输出文件\(/dev/stderr\): 文件描述符为2, linux 程序默认会将错误输出写入stderr 文件夹中

```bash
[admin@localhost shell]$ ll /dev/std*
lrwxrwxrwx. 1 root root 15 Jun 19 13:46 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx. 1 root root 15 Jun 19 13:46 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx. 1 root root 15 Jun 19 13:46 /dev/stdout -> /proc/self/fd/1
[admin@localhost shell]$
```

## 2.输出重定向

### 2.1 文件重定向

在进行shell 脚本编程的时候, 我们可能会将一些命令输出或者结果输出到指定文件中, 那么就可以使用输出重定向了.

** 常用重定向: **

| 语法 | 描述 |
| :--- | :--- |
| 命令 &gt; 文件 | 以覆盖方式, 将正确输出写入文件中 |
| 命令 &gt;&gt; 文件 | 以追加方式, 将正确输出写入文件中 |
| 命令 2&gt; 文件 | 以覆盖方式, 将命令错误输出写入文件中 |
| 命令 2&gt;&gt; 文件 | 以追加方式, 将命令错误输出写入文件中 |
| 命令 &&gt; 文件 | 已追加方式, 将命令正确&错误输出写入文件中 |
| 命令 &&gt;&gt; 文件 | 已追加方式，将命令正确＆错误输出写入文件中 |
| 命令 &gt;&gt; 文件1 &gt;&gt; 文件2 | 以追加方式，将命令正确输出写入文件１，将错误输出写入文件２ |

* > 表示以覆盖方式写入, 会先将原来的文件内容情况后写入
* > > 表示以追加方式写入, 不会修改原来的文件内容, 直接在原来文件末尾追加

### 2.2 特殊重定向

LInux 中很多命令都是有输出结果的, 有时我们希望不显示命令的输出结果, 但又不想将文件写入其它文件, 那么我们只需要将输出结果重定向到 /dev/null 文件即可. /dev/null 是一个特殊文件, 类似于linux 的黑洞一样.

```bash
[admin@localhost shell]$ ll /dev/null 
crw-rw-rw-. 1 root root 1, 3 Jun 19 13:46 /dev/null
[admin@localhost shell]$ [admin@localhost shell]$ ll /dev/null 
crw-rw-rw-. 1 root root 1, 3 Jun 19 13:46 /dev/null
[admin@localhost shell]$
```

## 3.示例程序

```bash
#!/bin/bash

#定义正确输出, 错误输出文件
f_std=info.txt
f_err=error.txt
f_all=all.txt

# 测试正确输出
echo "测试正确输出:" > $f_std
ls -d /s* >> $f_std

# 测试错误输出
echo "测试错误输出,清空文件:" > $f_err
ls /hh 2>> $f_err

# 测试正确错误输出
echo "测试全部输出, 清空文件" &> $f_all
ls -d /s* &>> $f_all
ls /hh &>> $f_all
```

** 测试输出: **

```bash
[admin@localhost shell]$ ./redirect.sh 
[admin@localhost shell]$ cat info.txt 
测试正确输出:
/sbin
/selinux
/srv
/sys
[admin@localhost shell]$ cat error.txt 
测试错误输出,清空文件:
ls: cannot access /hh: No such file or directory
[admin@localhost shell]$ cat all.txt 
测试全部输出, 清空文件
/sbin
/selinux
/srv
/sys
ls: cannot access /hh: No such file or directory
[admin@localhost shell]$
```



