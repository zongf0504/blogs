# linux 工具 -- rscpup

> 用于批量从上传文件至远程服务器, 依赖于expect 环境, 需要实现安装expect环境.


## 1. 工具特色

* 文件名支持通配符
* 没有用户交互,自动输入密码
* 可批量上传文件


## 2. rspup 开发

### 2.1 源程序 rspup .pl

```perl

#!/usr/bin/perl
#Desc: 批量上传文件到远程服务器. 此脚本依赖于expect 环境, 请先安装expect环境
#Args: 远程服务器ip, 用户名, 密码, 本地文件夹, 远程文件名列表, [expect 命令绝对路径]
#Auth: zonggf
#Date: 2017-07-11

use warnings;
use Term::ANSIColor qw(:constants);
$Term::ANSIColor::AUTORESET = 1;

######################################## 子程序  ########################################
#打印注释信息
sub print_help{
    #获取标签和注释
    my($tag) = shift @_;
    my($cmt) = shift @_;
    #输出第一行注释
    print BOLD BLUE "$tag: ";
    print "$cmt\n";
    #输出其它的注释
    printf "%5s $_\n","" foreach @_;
}


#检查是否是查询帮助
sub check_help{
  my $param = $ARGV[0];
  if("-h" eq $param || "--help" eq $param){
    &print_help("Desc","批量上传文件到远程服务器件,此脚本依赖于expect 环境, 需要先安装expect");
    &print_help("Args","参数列表: ip, 用户名, 密码, 远程目录, 本地文件名列表,支持通配符",
                "[expect 绝对路径地址] 可选参数");
    &print_help("Exam","rscpup root root /tmp ./hello*.txt /tmp/hello*.txt",
                "rscpup root root /tmp ./hello*.txt /tmp/hello*.txt /usr/bin/expect");
    &print_help("Auth","zongf");
    &print_help("Date","2017-07-17");
    exit;
  }
  #判断传入参数个数
  if(@ARGV < 5){
     print "[error] the param cannot less 5\n";
     exit;
  }elsif(@ARGV > 5){
     my $el = $ARGV[$#ARGV];
     if ($el =~ /^\/expect$/){
       $expect = pop @ARGV;
       unless( -e $expect){
     	print BOLD RED "The command $expect is not exsits !";
     	exit ;
       }
     }
  }
}

######################################## 主程序  ########################################

#校验帮助
&check_help;

#设置默认命令
$expect = "expect";

#获取脚本传入的ip, 用户名, 密码
$ip = shift @ARGV;
$user = shift @ARGV;
$password = shift @ARGV;
$remote_dir = shift @ARGV;
@local_files = @ARGV;

#拼接expect命令
$cmd = "$expect -c 'spawn scp @local_files $user\@$ip:$remote_dir\n"
      ."expect {\n"
      .'"*yes/no*" { send "yes\r"; exp_continue }' . "\n"
      ."\"*password:*\" { send \"$password\\r\" } \n"
      ."}\n"
      ."interact\n"
      ."exit\n"
      ."'";

# 执行命令
@files = `$cmd`;

# 处理返回结果
$flag = 0;
for my $el (@files){
   if( $el =~ /password:/ ){
      $flag = 1;
   }elsif ($flag == 1){
      #返回换行符为crlf, 即/r/n
      $el =~ s/\r\n/\n/g;
      #去除两侧空格
      $el =~ s/^\s+|\s+$//g;
      push (@results, $el);
   }
}

#处理返回结果
unless($#results == 0 && $results[0] =~ /cannot access/){
  for my $el (@results){
     unless($el =~ /^\s*$|:$/){
        $el =~ s/\s+/\t/g;
        print "$el\n";
     }
  }
}
```

### 2.2 加密为二进制程序

* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序, 并删除源程序 
* 将二进制程序rscpdown 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```bash
[admin@localhost perl]$ ls
rspup.pl
[admin@localhost perl]$ perl2bin -d rspup.pl 
开始转换脚本:rspup.pl
[admin@localhost perl]$ mv rspup /usr/local/bin/perl/
```

## 3. 测试

### 3.1 获取帮助信息

* 使用-h 或 --help 选项获取命令的帮助信息

```bash
[admin@localhost test]$ ./rscpup -h
Desc: 批量上传文件到远程服务器件,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, 远程目录, 本地文件名列表,支持通配符
      [expect 绝对路径地址] 可选参数
Exam: rscpup root root /tmp ./hello*.txt /tmp/hello*.txt
      rscpup root root /tmp ./hello*.txt /tmp/hello*.txt /usr/bin/expect
Auth: zongf
Date: 2017-07-17
[admin@localhost test]$ ./rscpup --help
Desc: 批量上传文件到远程服务器件,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, 远程目录, 本地文件名列表,支持通配符
      [expect 绝对路径地址] 可选参数
Exam: rscpup root root /tmp ./hello*.txt /tmp/hello*.txt
      rscpup root root /tmp ./hello*.txt /tmp/hello*.txt /usr/bin/expect
Auth: zongf
Date: 2017-07-17
```

### 3.2 批量上传文件至远程服务器
* 命令需要用单引号或者双引号包裹

```bash
[admin@localhost test]$ rscpup 192.168.145.100 admin admin /tmp ./hello.txt ./hello*.txt
hello.txt       0%      0       0.0KB/s --:--   ETA     hello.txt       100%    12      0.0KB/s 00:00
hello1.txt      0%      0       0.0KB/s --:--   ETA     hello1.txt      100%    12      0.0KB/s 00:00
hello.txt       0%      0       0.0KB/s --:--   ETA     hello.txt       100%    12      0.0KB/s 00:0
```










