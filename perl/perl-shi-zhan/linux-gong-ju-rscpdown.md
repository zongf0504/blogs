# linux 工具 -- rscpdown

> 用于批量从远程服务器上下载文件, 依赖于expect 环境, 需要实现安装expect环境.


## 1. 工具特色

* 文件名支持通配符
* 没有用户交互,自动输入密码
* 可批量下载文件


## 2. rscpdown 开发

### 2.1 源程序 rscpdown.pl

```perl
#!/usr/bin/perl
#Desc: 从远程服务器批量下载文件. 此脚本依赖于expect 环境, 请先安装expect环境
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
    &print_help("Desc","从远程服务器上批量下载文件,此脚本依赖于expect 环境, 需要先安装expect");
    &print_help("Args","参数列表: ip, 用户名, 密码, 本地目录, 远程文件名,支持通配符",
                "[expect 绝对路径地址] 可选参数");
    &print_help("Exam","rscpdown 192.168.145.100 admin admin . /tmp/hello*.txt",
                "rscpdown 192.168.145.100 admin admin  /tmp /tmp/hello*.txt /usr/bin/expect");
    &print_help("Auth","zongf");
    &print_help("Date","2017-07-17");
    exit;
  }
  #判断传入参数个数
  if(@ARGV < 5){
     print "[error] the param cannot less 5\n";
     exit;
  }elsif(@ARGV > 5){
     $expect = pop @ARGV;
     unless( -e $expect){
     	print BOLD RED "The command $expect is not exsits !";
     	exit ;
     }
  }
}

######################################## 主程序  ########################################

#校验帮助
&check_help;

#设置默认命令
$expect = "expect";

#获取脚本传入的ip, 用户名, 密码
($ip, $user, $password, $local_dir, $remote_files) = @ARGV;

#拼接expect命令
$cmd = "$expect -c 'spawn scp $user\@$ip:$remote_files $local_dir  \n"
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
rls.pl
[admin@localhost perl]$ perl2bin -d rscpdown.pl 
开始转换脚本:rscpdown.pl
[admin@localhost perl]$ mv rscpdown /usr/local/bin/perl/
```

## 3. 测试

### 3.1 获取帮助信息

* 使用-h 或 --help 选项获取命令的帮助信息

```bash
[admin@localhost test]$ rscpdown -h
Desc: 从远程服务器上批量下载文件,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, 本地目录, 远程文件名,支持通配符
      [expect 绝对路径地址] 可选参数
Exam: rscpdown 192.168.145.100 admin admin . /tmp/hello*.txt
      rscpdown 192.168.145.100 admin admin  /tmp /tmp/hello*.txt /usr/bin/expect
Auth: zongf
Date: 2017-07-17
[admin@gds localhost]$ rscpdown --help
Desc: 从远程服务器上批量下载文件,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, 本地目录, 远程文件名,支持通配符
      [expect 绝对路径地址] 可选参数
Exam: rscpdown 192.168.145.100 admin admin . /tmp/hello*.txt
      rscpdown 192.168.145.100 admin admin  /tmp /tmp/hello*.txt /usr/bin/expect
Auth: zongf
Date: 2017-07-17
```

### 3.2 获取远程服务器根目录文件列表
* 命令需要用单引号或者双引号包裹

```bash
[admin@localhost test]$ ./rls 192.168.145.100 admin admin '-d /s* /m*' /usr/bin/expect               
/media
/misc
/mnt
/sbin
/selinux
/srv
/sys
```

### 3.3 获取家目录列表

```bash
[admin@localhost test]$ ./rls 192.168.145.100 admin admin /home/admin /usr/bin/expect
blog
elk
jboss
jdk
zookeeper
```











