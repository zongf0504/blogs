# linux 工具 -- psnetstat
> 在日常工作中, 当遇到端口冲突时, 我们需要去查看占用此端口的进程. 通常我们会使用netstat 查询端口占用的进程id , 然后通过ps 去查看此进程id 的详细信息. 这个操作是比较麻烦的, 因此笔者开发了psnetstat 命令, 用于快速查询占用端口的进程详情

## 1. psnetstat 特使
* 使用方便, 一条命令就能快速定位占用端口号的进程
* 支持批量查询
* 只有root 用户才能执行, 因为只有root用户才能看看占用端口的进程id

## 2. psnetstat 工具开发

### 2.1 psnetstat.pl 源码
```perl
#!/usr/bin/perl
#Desc  查询本机监听端口号对应的服务
#Auth  zongf
#Date  2017-07-15

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
    
        &print_help("Desc","查询占用指定端口的应用程序, 只有root 用户可执行");
    &print_help("Args","一个或多个端口号");
    &print_help("Exam","psnetstat 8080: 查看占用8080 端口的进程信息",
                       "psnetstat 81 8080: 查看占用81 和 8080 端口的进程信息");
    &print_help("Auth","zongf");
    &print_help("Date","2017-07-15");
    exit;
  }else{
    #获取当前用户名
    chomp(my $user = `whoami` );
    if("root" ne $user){
      print BOLD RED "[error] "; print "只有root 用户才能执行\n";
      exit;
    }
  }
}

#格式化索引长度
#参数: 接收两个参数
#      para1: 需要格式化的索引
#      para2: 数组长度
#返回: [1   ]
sub fmt_idx{
    return shift @_ if @_ < 2;
    my ($str, $array_length) = @_;
    my $length = length $array_length;
    return sprintf "[%-${length}s] ", $str;
} 

######################################## 主程序  ########################################
#校验帮助
&check_help;

#进行端口号拼接
@patterns = @ARGV;
$ports = ":";
$ports .= shift @ARGV;
foreach (@ARGV){
  $ports .="|:$_";
}


#如果参数不为空，则进行筛选
$netstat_cmd = 'netstat -tlunp | grep LISTEN';
$netstat_cmd .= " | grep -E \"" . $ports ."\"" if defined $ports;
@lines = `$netstat_cmd`;

$lines_length = @lines;
foreach $idx (1 .. @lines){
    
    $line = $lines[$idx-1];

    #输出netstat 信息
    printf BOLD GREEN &fmt_idx($idx, $lines_length) . $line;

    #获取端口号
    $idx = index ($line , '/');
    $line = substr($line, 0, $idx);
    ($port,$net_pid) = (split(/\s+/,$line))[3,-1];

    #查询占用端口号的进程
    if($net_pid){
         @ps_lines =  `ps -ef | grep -v grep | grep -v /usr/bin/perl | grep $net_pid` if $net_pid;
         foreach(@ps_lines){
           $ps_pid = (split/\s+/, $_)[1];
           print if $ps_pid eq $net_pid;
         }
    }
    print "\n";
}
```

### 2.2 加密为二进制程序
* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序, 并删除源程序 
* 将二进制程序pskill 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```bin
[admin@localhost perl]$ ls
psnetstat.pl
[admin@localhost perl]$ perl2bin -d psnetstat.pl 
开始转换脚本:psnetstat.pl
[admin@localhost perl]$ mv psnetstat /usr/local/bin/perl/
```

## 3. 测试
## 3.1 查看命令帮助
```bash
[admin@localhost perl]$ psnetstat -h
Desc: 查询占用指定端口的应用程序, 只有root 用户可执行
Args: 一个或多个端口号
Exam: psnetstat 8080: 查看占用8080 端口的进程信息
      psnetstat 81 8080: 查看占用81 和 8080 端口的进程信息
Auth: zongf
Date: 2017-07-15
[admin@localhost perl]$ psnetstat --help
Desc: 查询占用指定端口的应用程序, 只有root 用户可执行
Args: 一个或多个端口号
Exam: psnetstat 8080: 查看占用8080 端口的进程信息
      psnetstat 81 8080: 查看占用81 和 8080 端口的进程信息
Auth: zongf
Date: 2017-07-15
```

## 3.2 非root 用户使用命令
* 使用非root 用户(笔者使用admin用户) 执行命令, 会弹出错误提示

```bash
[admin@localhost perl]$ psnetstat 81
[error] 只有root 用户才能执行
```

## 3.3 root 用户查看81 8080 端口
* 占用查询81 端口和 8080 端口的进程详情
* 每个结果为两行, 第一行为netstat 的输出, 第二行为 ps -ef 的输出

```perl
[root@localhost bin]# psnetstat 81 8080
[1] tcp        0      0 0.0.0.0:81                  0.0.0.0:*                   LISTEN      2974/nginx          
root       2974      1  0 00:27 ?        00:00:00 nginx: master process nginx

[2] tcp        0      0 :::8080                     :::*                        LISTEN      3827/java           
admin      3827      1  1 01:53 pts/1    00:00:08 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-8-8080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-8-8080/endorsed -classpath /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-8-8080 -Dcatalina.home=/opt/app/tomcat/tomcat-8-8080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-8-8080/temp org.apache.catalina.startup.Bootstrap start
```

