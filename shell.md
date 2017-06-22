# Shell 脚本语言

> Shell, 是一个用C 语言编写的程序, 它是用户操作Linux的桥梁, 也就是平时我们连接Linux 的所看到的命令行窗口. 平时我们输入的Linux 命令都是在Shell 程序中完成的.  
> Shell 脚本, 笔者认为Shell 脚本就是一组在Shell 程序中能执行的命令按顺序执行的集合.笔者通常使用shell不会太复杂的操作, 因为shell 脚本对于实现一些比较复杂的逻辑比较麻烦, 对于比较复杂的逻辑可以使用perl 或者 python 等其他脚本语言实现.

在Shell 程序窗口中, 通常我们只会输入简单的一行两行命令, 应该不会有人会直接在Shell 窗口中进行循环等复杂操作的吧, 而且输入的命令重用性不太好, 当想重复执行时, 还需要重新敲. 而Shell 脚本就可以很方便的解决这些问题.Shell 脚本能重复执行, 能写比较复杂的逻辑. 通常业界所说的shell 编程都指的是Shell 脚本编程.

## 1. shell 分类

Linux 的Shell 有很多种, 常见的有:

* bash: Bourne Shell
* sh: Bourne Again Shell
* csh: C Shell
* ksh: K Shell
* ...
  bash 是Linux 下的标准shell , 通常说的shell 也就是bash

## 2. Shell 入门程序

通常一说到入门程序, 肯定是输出一个Hello,world!, 当然了Shell 脚本也不例外.

### 2.1. 创建一个helloworld 脚本

使用vim 等文本编辑器, 创建一个helloword.sh 脚本,shell 脚本通常以后缀名.sh 结尾:

* \# 号表示注释, 通常将\# 号放在行首
* echo 为输出行命令, 会输出一行.
* shell 脚本不以分号为语句结束

```bash
#!/bin/bash

# 输出hello,world
echo "hello,world!"
```

### 2.2 修改脚本权限

在Linux 中默认新建文件是不具备可执行权限的, 而脚本要想运行需要可执行权限. 所以首先需要为脚本赋予可执行权限.通常我们会为脚本赋予755 权限, 755 权限含义:

* 用户: 可读, 可写, 可执行
* 用户组: 可读, 可执行
* 其他人: 可读, 可执行

```bash
[admin@localhost shell]$ chmod 755 helloworld.sh 
[admin@localhost shell]$ ll helloworld.sh 
-rwxr-xr-x. 1 admin admin 33 Jun 21 10:12 helloworld.sh
[admin@localhost shell]$
```

### 2.3 执行脚本

脚本执行时, 必须使用绝对路径,或者相对路径, 只输入脚本名称是不能执行的

** 方式一: **

```bash
[admin@localhost shell]$ ls
helloword.sh
[admin@localhost shell]$ ./helloword.sh 
hello,world!
[admin@localhost shell]$
```

** 方式二: **

```bash
[admin@localhost ~]$ ls
shell
[admin@localhost ~]$ /home/admin/shell/helloword.sh 
hello,world!
[admin@localhost ~]$
```

** 错误方式: **

```bash
[admin@localhost shell]$ ls
helloword.sh
[admin@localhost shell]$ helloword.sh
-bash: helloword.sh: command not found
[admin@localhost shell]$
```



