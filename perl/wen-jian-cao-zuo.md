# Perl 文件操作
> Perl 对文件和目录操作提供了相关的增删改查API, 如rename, rmdir, unlink 等函数, 但是笔者认为并没有太大必要去花时间掌握这些函数, 因为perl 通过system 函数可以直接执行系统命令, 所以直接使用system+原生linux文件操作命令即可, 这样可以少学一套API. 因此, 笔者更常用于此种方式.


#1. 文件内容读写

## 1.1 文件句柄
* perl 程序通过文件句柄和文件进行文件读写
* 文件句柄: 文件句柄相当于一个指针, 指向这个文件; 句柄必须是全部大写或者为标量, 笔者建议句柄会用标量, 因为perl默认的文件句柄为全部大写.
* perl 内置句柄有: STDIN, STDOUT, STDERROR 等
* perl 文件打开后未关闭的话, 在程序借宿时会自动关闭

### 1.1.1 打开文件句柄
* 语法: open 句柄名称, 打开方式, 文件名;

常用打开方式:

| 打开方式 | 含义 |
| :--- | :--- |
| < | 以输入方式打开文件, 用于读取文件内容  |
| > | 以输出方式打开文件, 用于写文件, 打开的同时会清空文件内容 |\
| >> | 以输出方式打开文件, 用于写文件, 打开时不清空文件, 直接在文件末尾追加内容 |
| >:encoding(UTF-8) | 以指定编码打开文件, 可用于以上三种方式 |

### 1.1.2 关闭文件句柄
* 语法: close 文件句柄名称;
* 当文件不使用的时候建议关闭文件句柄, 减少资源占用.若不手动关闭的话, 当程序结束时, 会自动关闭.

## 1.2 读文件
* 当文件比较小的时候, 一次性读取文件所有内容效率更高; 当文件较大时, 可以一行一行读.

### 1.2.1 读取文件全部内容
```perl
#!/usr/bin/perl

$filename = "/etc/passwd";

#打开文件
open $file, "<", $filename or "cannot open file:$filename\n";

#读取文件全部内容
@lines = <$file>;

#关闭文件
close $file;

#输出文件
print "$_" foreach @lines;
```

### 1.2.2 逐行读取
* 逐行读取适合读取大文件

```perl
#!/usr/bin/perl

$filename = "/etc/passwd";

#打开文件
open $file, "<", $filename or "cannot open file:$filename\n";

#逐行读取文件内容
while(<$file>){
   print "$_";
}

#关闭文件
close $file
```

## 1.3 写文件
* 将程序处理结果写入文件是非常常用的一个操作.
* 写入文件可以使用print, printf输出语句, 只需要在print/printf后面指定文件句柄即可. 默认句柄为STDOUT, 即标准输出.

### 1.3.1 清空方式写入文件
* 重复执行两次此脚本, 会发现tmp.txt 文件中始终都是一行内容

```perl
#!/usr/bin/perl

$filename = "tmp.txt";

#打开文件:清空方式, 打开的同时会清空文件
open $file, ">", $filename or "cannot open file:$filename\n";

#输出字符串到文件中
print $file "hello,world\n";

#关闭文件句柄
close $file;

```

### 1.3.2 追加方式写入文件
* 重复执行脚本多次, 没执行一次, tmp.txt 文件中新增一行hello,world

```perl
#!/usr/bin/perl

$filename = "tmp.txt";

#打开文件:清空方式, 打开的同时会清空文件
open $file, ">>", $filename or "cannot open file:$filename\n";

#输出字符串到文件中
print $file "hello,world\n";

#关闭文件句柄
close $file;

```

### 1.3.3 重新向标准输出
* 当文件只需要向一个文件输出内容时,可以直接将标准输出重定向,这样就不用每次在print语句后面添加文件句柄了

```perl
#!/usr/bin/perl

$filename = "tmp.txt";

#打开文件:清空方式, 打开的同时会清空文件
open STDOUT, ">", $filename or "cannot open file:$filename\n";

#输出字符串到文件中
print "hello,world\n";

```

# 2. 文件检测

## 2.1 常用文件操作符
* perl 提供了很多用于文件判断的操作符, 笔者只列出个人常用的文件操作符:

### 2.1.1 判断文件权限
| 测试操作符 | 含义 |
| :--- | :--- |
| -r | 当前用户对此文件是否拥有可读权限 |
| -w | 当前用户对此文件是否拥有可写权限 |
| -x | 当前用户对此文件是否拥有可执行权限 |
| -o | 此文件所有者是否是当前用户 |

### 2.1.2 判断文件类型
| 测试操作符 | 含义 |
| :--- | :--- |
| -f | 文件是否是普通文件 |
| -d | 文件是否是目录文件 |
| -l | 文件是否是链接文件 |

### 2.1.3 判断文件存在
| 测试操作符 | 含义 |
| :--- | :--- |
| -e | 文件或目录存在 |
| -z | 判断文件内容为空,不能用于判断目录 |
| -s | 判断文件或目录存在, 返回文件或目录的大小, 单位为字节 |

## 2.2 文件操作符组合
* 通常我们可能会对同一个文件的多个属性进行判断, 那么就需要组合文件操作符

### 2.2.1 逻辑表达式组合
* perl 可以使用and 和 or 表示逻辑与和逻辑或的关系
* 隐式文件句柄 _ 表示上次监测的文件, 用于提升效率

```perl
if ( -f $file and -x _ ) {
   print "文件$file 存在且可执行\n";
}
```

### 2.2.2 栈式组合
* 栈操作符只能表示逻辑与关系, 使用起来很方便

```perl
if( -f -x $file){
   print "文件$file 存在且可执行\n";
}
```

# 3. 文件夹操作
* perl语言中对文件夹和文件操作是很简单的, 通常只要拿到文件绝对路径名称即可, 然后使用系统命令对文件进行增删改查即可.

## 3.1 获取目录下所有文件
* perl 获取目录下所有文件操作是极其简单的,简直不能再简单了
* 通过砖石操作符获取的只是文件名, 返回文件名为局对路径文件名
* 支持shell的通配符, 如*, ?, []等

```perl
# 语法: <目录名/*>, 支持shell的通配符, 如: @plfiles=<$dir/*.pl>; 或 @files=<$dir/*>K;

#输出home目录下所有文件
$dir="/home";
@files=<$dir/*>;
print "$_\n" foreach @files;

```

## 3.2 递归文件夹
### 3.2.1 普通方式

```perl
#!/usr/bin/perl

#递归函数
sub recure{
  #获取传入参数:文件名
  my $file = shift @_;

  #判断文件是否是目录
  if( -d $file ){
     #文件是目录, 则文件夹数量+1
     $cnt_dir++;

     #获取文件夹下所有文件
     my @sub_files = <$file/*>;
     
     #遍历所有文件, 递归方法 
     foreach my $sub_file (@sub_files ){
        &recure($sub_file);
     }
   }else {
     #如果文件是普通文件,则文件数量+1
     $cnt_file++;
   }
}

$dir = "/home/admin/blog";
&recure($dir);
print "files:$cnt_file, dirs:$cnt_dir\n";

```

### 3.2.2 极简方式
* 从简化模式可以看出, perl是非常灵活的

```perl
#!/usr/bin/perl

#递归函数
sub recure{
  #获取传入参数:文件名
  my $file = shift @_;

  #判断文件是否是目录
  if( -d $file ){
     #文件是目录, 则文件夹数量+1
     $cnt_dir++;

     #遍历所有文件, 递归方法 
     &recure($_) foreach <$file/*>;
   }else {
     #如果文件是普通文件,则文件数量+1
     $cnt_file++;
   }
}

$dir = "/home/admin/blog";
&recure($dir);
print "files:$cnt_file, dirs:$cnt_dir\n";
```









