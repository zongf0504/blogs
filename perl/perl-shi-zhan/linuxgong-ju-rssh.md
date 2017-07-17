# linux 工具 -- rssh

> 在写自动化脚本的时候, 有时候会涉及到向远程服务器发送命令这种需求, 此时我们可以通过ssh 命令来实现. 但是ssh存在一次交互工程, 需要手工输入密码, 这样式很不方便的.因此笔者封装了一个rssh 工具, 用来执行简单的远程命令.


## 1. 工具特色

* 自动输入ssh密码, 无须人工交互
* 可嵌入脚本运行


## 2. rssh 开发

### 2.1 源程序 confview.pl

```perl
#!/usr/bin/perl
#Desc 向远程服务器发送一条命令
#Args: 远程服务器ip, 用户名, 密码, 命令, [expect 命令绝对路径]
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
  	&print_help("Desc","向远处linux服务器执行一条命令,返回命令执行的结果.此脚本依赖于expect 环境,需要实现安装");
    &print_help("Args","参数列表: ip, 用户名, 密码, 命令",
                "[expect 绝对路径地址] 可选参数");
    &print_help("Exam","rssh 192.168.145.100 admin admin 'ls /'",
                "rssh 192.168.145.100 admin admin 'ls /' /usr/bin/expect");
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

######################################## 主程序  ########################################

#校验帮助
&check_help;

#设置默认命令
$expect = "expect";

#获取脚本传入的ip, 用户名, 密码
($ip, $user, $password, $cmd) = @ARGV;

#拼接命令
$cmd = "$expect -c 'spawn ssh $user\@$ip \"$cmd\" \n"
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

#输出返回结果
print "$_\n" foreach @results;
```

### 2.2 加密为二进制程序

* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序, 并删除源程序 
* 将二进制程序rssh 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```bash
[admin@localhost perl]$ ls
rssh.pl
[admin@localhost perl]$ perl2bin rssh.pl 
开始转换脚本:rssh.pl
[admin@localhost perl]$ mv rssh /usr/local/bin/perl/
```

## 3. 测试

### 3.1 获取帮助信息

* 使用-h 或 --help 选项获取命令的帮助信息

```bash
[admin@localhost test]$ ./rssh -h
Desc: 向远处linux服务器执行一条命令,返回命令执行的结果.此脚本依赖于expect 环境,需要实现安装
Args: 参数列表: ip, 用户名, 密码, 命令
      [expect 绝对路径地址] 可选参数
Exam: rssh 192.168.145.100 admin admin 'ls /'
      rssh 192.168.145.100 admin admin 'ls /' /usr/bin/expect
Auth: zongf
Date: 2017-07-17
[admin@gds test]$ ./rssh --help
Desc: 向远处linux服务器执行一条命令,返回命令执行的结果.此脚本依赖于expect 环境,需要实现安装
Args: 参数列表: ip, 用户名, 密码, 命令
      [expect 绝对路径地址] 可选参数
Exam: rssh 192.168.145.100 admin admin 'ls /'
      rssh 192.168.145.100 admin admin 'ls /' /usr/bin/expect
Auth: zongf
Date: 2017-07-17
```

### 3.2 获取远程服务器根目录文件列表
* 命令需要用单引号或者双引号包裹

```bash
[admin@localhost test]$ ./rssh 192.168.145.100 admin admin 'ls -d /m* /n*' /usr/bin/expect
/media
/misc
/mnt
/net
/nohup.out
```




