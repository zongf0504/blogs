# Perl 进程管理
> Linux 系统中, 每执行一条命令或运行一个程序,都会产生一个主进程, 在主进程运行的时候,也有可能会产生多个子进程, 我们可以通过命令: ps -ef | grep xxx 来查看进程相关的信息.但是对于有些命令, 执行速度很快, 比如说ls /, 这样我们通过ps  -ef 并不能看到与ls 相关的进程信息, 虽然看不到, 但是这不能表示ls 在执行的时候不会产生进程, 只是因为执行速度太快,难以捕获罢了.

perl 对进程的管理功能也是比较强大的, 但是笔者用perl 只是为了写一些脚本程序, 所以只需要掌握以下内容就行了:
* perl 程序中调用系统命令或执行其它系统脚本
* perl 向其它进程发送信号(关闭其它进程)
* perl 接收其它进程发送的信号(被Ctrl+C 中断程序执行)


# 1. 调用系统命令或执行其它程序
perl 语言中调用执行系统命令或其它程序时会创建一个新的子进程, 执行命令有三种模式: system, qx, exec
* 父进程: perl 脚本所占用的进程为父进程, 又称主进程
* 子进程: perl 脚本调用系统命令或执行其它程序占用的进程

| 命令 | 格式 | 描述 |
| :--- | :--- | :--- |
| system | 主进程休眠,启动子进程执行其它程序,不捕获输出 | 返回状态码, 0 为成功, 其它为失败 |
| qx / `` | 主进程休眠, 启动子进程执行其它程序, 捕获输出 | 返回子进程标准输出的内容 |
| exec | 主进程结束, 启动子进程执行其它程序, 不捕获输出| 无返回值 |

## 1.1 system
* 命令格式1: system " xxx "; 双引号中的$ 引用的是perl标量, \$ 引用的是shell 的系统环境变量
* 命令格式2: system ' xxx '; 单引号中的$ 引用的是shell 中的环境变量, \$ 无实用意义

```perl
#输出当前系统时间
system "echo date";
#输出perl标量
system "echo $JAVA_HOME";
#输出环境变量
system "echo \$JAVA_HOME";
#输出环境变量
system 'echo $JAVA_HOME';
```

## 1.2 qx
反引号`` 是qx 的简写形式, qx 可以用(), "", '' 等作为限定符, 返回值可由标量或数组进行捕获.在标量上下文中输出的内容为一个字符串, 在数组上下文中, 按换行符分隔为一个一个的元素.

* 命令格式1:  $line = `xxx`; 反引号中$ 引用的是perl 标量, \$ 引用的是shell 系统环境变量
* 命令格式2: $line = qx ' xxx '; qx 单引号中$引用的是shell 系统环境变量, \$ 无实用意义 
* 命令格式3: @lines = qx " xxx "; qx 双引号中$引用的是perl 变量, \$ 无实用意义
* 命令格式4: @lines = qx ( xxx ); qx 双引号中$引用的是perl 变量, \$ 无实用意义

```perl
$JAVA_HOME = qx' echo $JAVA_HOME ';
print "shell:JAVA_HOME=$JAVA_HOME ";

$JAVA_HOME = qx ( echo \$JAVA_HOME );
print "shell:JAVA_HOME=$JAVA_HOME ";

$JAVA_HOME = `echo \$JAVA_HOME`;
print "shell:JAVA_HOME:$JAVA_HOME ";
```

## 1.3 exec
* exec 启动子进程后, 主进程会结束, 所以exec 通常是脚本中的最后一行代码
* exec 常用于为子程序设置运行环境, 可通过直接修改%ENV 哈希中的环境变量直接修改运行环境, 修改的系统环境变量会影响其启动的子进程,修改的环境变量只在当前脚本中有效, 不会对真正的系统环境变量有所影响.

```perl
# 为子程序设置环境变量, 指定jdk 环境
$ENV{JAVA_HOME} = '/opt/app/tomcat/jdk/jdk1.8_0131';

# 启动tomcat
exec '/opt/app/tomcat/tomcat-8-8080/bin/startup.sh &';
```


# 2. 向其它进程发送信号
* 命令格式: kill sigInt, pid1 pid2 ...;
* sigInt: 信号数字, 常用的是9, 强制杀死进程
* pid: 进程id , 可同时杀死多个进程
* 返回成功杀死进程的数量

```perl
kill 9, 5161 6161;
```

# 3. 接收其它进程发送的信号
* perl 程序运行时, 也会启动一个进程, 所以perl 进程也可以接收其它进程发送的信号
* perl 内置了%SIG 存储了perl 可接收的所有信号参数, 存储格式为: key=method
* 我们只需要给对应的key对应的value设置相应的方法即可,这样当程序接收到信号,就会执行对应的方法.
* 注意: 当perl 主程序接收到相应的信号之后,会暂时中断目前正在执行的流程, 而去执行信号对应的方法, 当方法完成之后,再回到原来执行的位置,继续执行.
* 信号方法的注册必须在脚本程序最前面,因为随时都可能执行.

```perl
#必须在程序最开始设置, 设置接收到ctrl + C 信号时执行的方法
$SIG{'INT'} = 'clean';

#接收到ctrl+C 信号时处理方法
sub clean{
     print 'excute clean operation...';
     exit;  #接收到信号时，需要手工调用exit 函数，否则程序不会退出
}
```

# 4. 测试用例
* 测试脚本运行的时候, 需要启动两个shell 窗口, 一个窗口执行脚本, 另一个窗口监控进程
* 脚本名称为proce.pl, 如果修改脚本名称的话, 查看进程的命令需要修改
= 脚本运行中, 需要配合使用Ctrl+C 来向进程中发送终止信号.

## 4.1 测试脚本
```perl
#!/usr/bin/perl

#指定程序接收到Ctrl+C 信号时最后执行的方法clean,必须放在程序最前面
#INT 信号代表从键盘输入Ctrl+C结束程序
$SIG{'INT'} = 'clean';

#定义clean方法
sub clean {
   print "程序异常结束, 执行清理操作...\n";
   
   #显示调用退出函数, 否则会从接收到Ctrl+C信号的地方继续执行行
   #exit;
}


print "\n#################### 1.1 system 调用其它程序  ####################\n";
$JAVA_HOME = "java home";

print "perl  JAVA_HOME:"; system "echo $JAVA_HOME";
print "shell JAVA_HOME:"; system "echo \$JAVA_HOME";
print "shell JAVA_HOME:"; system 'echo $JAVA_HOME';

print "\n#################### system 返回结果  ####################\n";
$code = system "date";
print "正确执行返回值: $code \n";

$code = system "hello";
print "错误执行返回值: $code\n";

print "\n############# 1.2 qx()/`` 捕获系统输出 ##################\n";

$JAVA_HOME = qx' echo $JAVA_HOME ';
print "shell:JAVA_HOME=$JAVA_HOME ";

$JAVA_HOME = qx ( echo \$JAVA_HOME );
print "shell:JAVA_HOME=$JAVA_HOME ";

$JAVA_HOME = `echo \$JAVA_HOME`;
print "shell:JAVA_HOME:$JAVA_HOME ";

print "\n####################  1.3 修改子程序的环境变量 ################\n";
#通过修改ENV哈希中的值, 直接影响子程序的系统环境变量
$ENV{JAVA_HOME}="JAVA_HOME";

print "子程序:JAVA_HOME:"; system 'echo $JAVA_HOME';

print "程序即将休眠30秒,请在另外窗口中执行命令查看进程, 查看后使用Ctrl+C 发送中断信号\n";
print "查看命令:ps -ef | grep -v grep | grep -E \"process.pl|sleep\"\n";
system "sleep 30000";
print "\n####################  接收Ctrl + C 信号 ################\n";

print "\n####################  exec  ################\n";
print "程序即将终止主进程, 并进入exec 进程,请在另外窗口中执行命令查看剩余进程, 查看后使用Ctrl+C 发送中断信号\n";
print "查看命令:ps -ef | grep -v grep | grep -E \"process.pl|sleep\"\n";
exec "sleep 100000 \n";

```

## 4.2 脚本输出
```bash
[admin@localhost perl]$ ./process.pl 

#################### 1.1 system 调用其它程序  ####################
perl  JAVA_HOME:java home
shell JAVA_HOME:/opt/app/jdk/jdk1.6.0_31
shell JAVA_HOME:/opt/app/jdk/jdk1.6.0_31

#################### system 返回结果  ####################
Thu Jul  6 13:21:54 CST 2017
正确执行返回值: 0 
错误执行返回值: -1

############# 1.2 qx()/`` 捕获系统输出 ##################
shell:JAVA_HOME=/opt/app/jdk/jdk1.6.0_31
 shell:JAVA_HOME=/opt/app/jdk/jdk1.6.0_31
 shell:JAVA_HOME:/opt/app/jdk/jdk1.6.0_31
 
####################  1.3 修改子程序的环境变量 ################
子程序:JAVA_HOME:JAVA_HOME
程序即将休眠30秒,请在另外窗口中执行命令查看进程, 查看后使用Ctrl+C 发送中断信号
查看命令:ps -ef | grep -v grep | grep -E "process.pl|sleep"
^C
####################  接收Ctrl + C 信号 ################

####################  exec  ################
程序即将终止主进程, 并进入exec 进程,请在另外窗口中执行命令查看剩余进程, 查看后使用Ctrl+C 发送中断信号
查看命令:ps -ef | grep -v grep | grep -E "process.pl|sleep"
^C
```

## 4.3 第一次暂停时查看系统进程
* 有两个进程, 一个是主进程, 一个是system 启动的休眠子进程 

```bash
[admin@localhost ~]$ ps -ef | grep -v grep | grep -E "process.pl|sleep"
admin     4201 32469  0 13:20 pts/1    00:00:00 /usr/bin/perl ./process.pl
admin     4218  4201  0 13:20 pts/1    00:00:00 sleep 30000
```

## 4.2 第二次暂停时, 查看系统进程
* 只有一个进程, 主进程结束, 只剩下由exec 启动的休眠子进程.

```bash
[admin@localhost ~]$ ps -ef | grep -v grep | grep -E "process.pl|sleep"
admin     4201 32469  0 13:20 pts/1    00:00:00 sleep 100000
```












