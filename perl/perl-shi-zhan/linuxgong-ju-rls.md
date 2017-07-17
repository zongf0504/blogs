# linux 工具 -- rls

> 用于获取远程linux服务器目录下的文件列表.


## 1. 工具特色

* 目录名支持通配符
* 可输入多个路径,会自动合并结果
* 可选ls 的参数, 如-d , -l 等


## 2. rls 开发

### 2.1 源程序 rls.pl

```perl
#!/usr/bin/perl
#Desc: 获取远程服务器文件列表. 此脚本依赖于expect 环境, 请先安装expect环境
#Args: 远程服务器ip, 用户名, 密码, 远程文件名, [expect 命令绝对路径]
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
    &print_help("Desc","获取远程服务器文件列表, 相当于向远程服务器执行ls 命令, 若没有文件则返回为空,此脚本依赖于expect 环境, 需要先安装expect");
    &print_help("Args","参数列表: ip, 用户名, 密码, \"文件名列表,支持通配符\", 文件名列表需要用单引号或双引号包裹",
                "[expect 绝对路径地址] 可选参数");
    &print_help("Exam","rls 192.168.145.100 admin admin '-d /s* /m*'",
                "rls 192.168.145.100 admin admin  '-d /s* /m*' /usr/bin/expect",
                "rls 192.168.145.100 admin admin  '-d /s* /m*' /usr/bin/expect");
    &print_help("Auth","zongf");
    &print_help("Date","2017-07-17");
    
    exit;
  }
  #判断传入参数个数
  if(@ARGV < 4){
     print "[error] the param cannot less 4\n";
     exit;
  }elsif(@ARGV > 4){
     $expect = pop @ARGV;
     unless( -e $expect){
     	print BOLD RED "The command $expect is not exsits !";
     	exit ;
     }
  }
}

#################### 主程序  ####################

#校验帮助
&check_help;

#设置默认命令
$expect = "expect";

#获取脚本传入的ip, 用户名, 密码
($ip, $user, $password, $cmd) = @ARGV;

#$para = "-d" if "@ARGV" =~ /\*/ ;

#拼接命令
$cmd = "$expect -c 'spawn ssh $user\@$ip \"ls $cmd\" \n"
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
* 将二进制程序rls 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```bash
[admin@localhost perl]$ ls
rls.pl
[admin@localhost perl]$ perl2bin -d rls.pl 
开始转换脚本:rls.pl
[admin@localhost perl]$ mv rls /usr/local/bin/perl/
```

## 3. 测试

### 3.1 获取帮助信息

* 使用-h 或 --help 选项获取命令的帮助信息

```bash
[admin@localhost test]$ rls -h
Desc: 获取远程服务器文件列表, 相当于向远程服务器执行ls 命令, 若没有文件则返回为空,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, "文件名列表,支持通配符", 文件名列表需要用单引号或双引号包裹
      [expect 绝对路径地址] 可选参数
Exam: rls 192.168.145.100 admin admin '-d /s* /m*'
      rls 192.168.145.100 admin admin  '-d /s* /m*' /usr/bin/expect
      rls 192.168.145.100 admin admin  '-d /s* /m*' /usr/bin/expect
Auth: zongf
Date: 2017-07-17
[admin@localhost test]$ rls --help
Desc: 获取远程服务器文件列表, 相当于向远程服务器执行ls 命令, 若没有文件则返回为空,此脚本依赖于expect 环境, 需要先安装expect
Args: 参数列表: ip, 用户名, 密码, "文件名列表,支持通配符", 文件名列表需要用单引号或双引号包裹
      [expect 绝对路径地址] 可选参数
Exam: rls 192.168.145.100 admin admin '-d /s* /m*'
      rls 192.168.145.100 admin admin  '-d /s* /m*' /usr/bin/expect
      rls 192.168.145.100 admin admin  '-d /s* /m*' /usr/bin/expect
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





