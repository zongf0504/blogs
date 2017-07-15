# linux 工具 -- pskill
> 在linux 中如果需要强制杀死进程, 通常需要先通过ps 命令查询到该进程id, 然后在通过kill 命令强制杀死进程. 在频繁地linux 运维管理过程中, 这些操作无疑是繁琐的, 因此笔者开发了pskill 命令, 用于快速杀死进程.


## 1. pskill 特色
* 使用方便, 结合了ps 和 kill 命令, 一步即可
* 提供用户交互, 让用户选择要删除的进程
* 防止误操作, 有最大进程限制

## 2. pskill 开发

### 2.1 pskill.pl 源码:

```perl
#!/usr/bin/perl
#Desc  强制杀死正在运行的进程,可同时杀死多个进程
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
    &print_help("Desc","强制杀死当前正在运行的进程, 存在一次用户交互");
    &print_help("Args","关键字列表");
    &print_help("Exam","pskill tomcat: 选择杀死包含tomcat 关键字的进程",
                "pskill tomcat nginx: 选择杀死包含tomcat 或 nginx 关键字的进程");
    &print_help("Auth","zongf");
    &print_help("Date","2017-07-15");

    exit;
  }
}

#desc    输出有颜色的字符串
#para1  接收至少两个参数以上，第一个参数为要格式化的字符串，之后的参数为要使用颜色的字符串
sub print_color(){
  # 如果长度小于2, 那么不进行格式化输出，直接打印
  if(@_ < 2){
     print shift @_;
     return;
  }

  #获取要格式化颜色的字符串
  my $line = shift @_;

  #获取要高亮的字符串数组
  my @patterns = @_;

  #获取要高亮显示的字符串数组，拼接正则模式
  my $spectors = (shift @_) . '+';
  foreach(@_){
     $spectors .= "|$_+";
  }
  #按正则模式进行分组
  my @arrays = split(/($spectors)/, $line);

  #输出结果
  for my $item(@arrays){
      #直接使用@patterns 数组反向匹配，数组内插时，每个字符串直接会有空格
      my $pattern = "@patterns";
      
      #字符串中查找元素，如果有的话则高亮显示，否则正常显示
      my $idx = index($pattern, $item);
      if($idx > -1){
        print BOLD RED $item;
      }else{
        print $item;
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

#杀死进程
sub kill_ps_line{
  my $ps_line = shift @_;
  my $pid = (split/\s+/,$ps_line)[1];
  $cnt = kill 9, $pid;
  if($cnt == 1){
    print BOLD GREEN "[成功] "; printf "进程pid: %6d 强制杀死成功!\n",$pid;
  } else {
    print BOLD RED "[失败] "; printf "进程pid:%6d 强制杀失败!\n",$pid;
  }
}

#获取用户输入，返回输入列表
sub get_user_choose{

  print "\n[输入] 请输入您要杀死的进程序号，0表示所有:  ";
  chomp($choose=<STDIN>);

  #判断用户输入是否只能是数字
  unless($choose =~ /^(\s|[0-9]|\s)+$/){
    print "[错误] 只能输入正整数\n";
    exit;
  }
  
  #获取用户输入的序号列表
  @ids = split(/\s+/, $choose);
  @ids_asc = sort @ids;

  # 判断输入是否包含0,确保0 只能单独出现
  if(@ids_asc >1 && $ids_asc[0] ==0 ){
     print "[错误] 0 表示全部, 只能单独使用\n";
     exit;
  }

  #判断用户输入最大值最小值
  if($ids_asc[-1] > (@lines +1) || $ids_asc[0] < 0){
     print "[错误] 序号取值范围为 [0~ " . (@lines) . "]\n";
     exit;
  }
  
  #如果只有一个元素，且为0 
  if(@ids == 1 && $ids_asc[0] ==0){
    return (1 .. @lines);
  }else{
    return @ids;
  }
}

######################################## 主程序  ########################################
&check_help;

#如果用户不传过滤参数，则直接退出程序
if(@ARGV <1){
  print BOLD RED "[error] "; print "此命令需要至少一个参数作为筛选条件!\n";
  exit;
}

#存储匹配模式，用户高亮输出
@patterns = @ARGV;

#进行端口号拼接
$names = shift @ARGV;
foreach (@ARGV){
  $names .="|$_";
}

#如果参数不为空，则进行筛选
$ps_cmd = 'ps -ef | grep -v grep | grep -v kill9';
$ps_cmd .= " | grep -E \"" . $names ."\"" if defined $names;

@lines = `$ps_cmd`;
$length =@lines;

if($length < 1){
  #没有符合条件时,直接退出程序
  print "没有符合条件的进程运行\n";
  exit;
} elsif ($length >10 ){
  #做多一次性杀死10个进程
  print "正在运行的进程:\n";
  print foreach @lines;
  printf "最多一次性杀死10个进程，而符合筛选条件的进程数为: %3d个，请精确筛选条件\n", $length;
  exit;
} else{
  #如果查出结果大于1个进程，则需要确认
  
  #遍历查询结果
  $lines_length = @lines;
  for $idx (1 ... @lines){
    print BOLD GREEN &fmt_idx($idx, $lines_length);
    &print_color($lines[$idx-1], @patterns);
  }

  #获取用户选择的序号列表
  @ids = &get_user_choose; 

  #按用户选择进行杀死进程
  &kill_ps_line($lines[$_-1]) foreach @ids;
}

```

### 2.2 加密为二级制程序
* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序, 并删除源程序
* 将二进制程序pskill 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```perl
[admin@localhost perl]$ ls
pskill.pl
[admin@localhost perl]$ perl2bin pskill.pl 
开始转换脚本:pskill.pl
[admin@localhost perl]$ ls
pskill  pskill.pl
[admin@localhost perl]$ mv pskill /usr/local/bin/perl/
```

## 3. 测试
* pskill 工具会有一次用户交互过程, 需要用户选择要删除的索引号, 0 代表删除列出的所有进程
* pskill 为防止误操作, 所以限制每次最多删除10个进程

### 3.1 查看命令帮助
```bash
[admin@localhost perl]$ pskill -h
Desc: 强制杀死当前正在运行的进程, 存在一次用户交互
Args: 关键字列表
Exam: pskill tomcat: 选择杀死包含tomcat 关键字的进程
      pskill tomcat nginx: 选择杀死包含tomcat 或 nginx 关键字的进程
Auth: zongf
Date: 2017-07-15
[admin@localhost perl]$ pskill --help
Desc: 强制杀死当前正在运行的进程, 存在一次用户交互
Args: 关键字列表
Exam: pskill tomcat: 选择杀死包含tomcat 关键字的进程
      pskill tomcat nginx: 选择杀死包含tomcat 或 nginx 关键字的进程
Auth: zongf
Date: 2017-07-15
```

### 3.2. 删除单个进程
* 查询并选择杀死tomcat 进程
* 用户交互: 输入列表索引

```bash
[admin@localhost perl]$ pskill tomcat
[1] admin      3383      1 49 01:27 pts/1    00:00:06 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-7-7080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xms516m -Xmx1024m -Xss1024K -XX:PermSize=512m -XX:MaxPermSize=512m -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-7-7080/endorsed -classpath /opt/app/tomcat/tomcat-7-7080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-7-7080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-7-7080 -Dcatalina.home=/opt/app/tomcat/tomcat-7-7080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-7-7080/temp org.apache.catalina.startup.Bootstrap start
[2] admin      3429      1 84 01:27 pts/1    00:00:05 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-8-8080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-8-8080/endorsed -classpath /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-8-8080 -Dcatalina.home=/opt/app/tomcat/tomcat-8-8080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-8-8080/temp org.apache.catalina.startup.Bootstrap start

[输入] 请输入您要杀死的进程序号，0表示所有:  1          
[成功] 进程pid: %6d 强制杀死成功!
```

### 3.3 批量删除进程
```bash
[admin@localhost perl]$ pskill tomcat
[1] admin      3521      1 49 01:29 pts/1    00:00:04 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-7-7080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xms516m -Xmx1024m -Xss1024K -XX:PermSize=512m -XX:MaxPermSize=512m -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-7-7080/endorsed -classpath /opt/app/tomcat/tomcat-7-7080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-7-7080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-7-7080 -Dcatalina.home=/opt/app/tomcat/tomcat-7-7080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-7-7080/temp org.apache.catalina.startup.Bootstrap start
[2] admin      3542      1 52 01:29 pts/1    00:00:03 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-8-8080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-8-8080/endorsed -classpath /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-8-8080 -Dcatalina.home=/opt/app/tomcat/tomcat-8-8080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-8-8080/temp org.apache.catalina.startup.Bootstrap start
[3] admin      3564   1656  0 01:30 pts/1    00:00:00 /usr/bin/perl ./pskill tomcat

[输入] 请输入您要杀死的进程序号，0表示所有:  1 2
[成功] 进程pid:   3521 强制杀死成功!
[成功] 进程pid:   3542 强制杀死成功!
```

### 3.4 杀死全部进程
* 用户交互时输入0, 表示删除查询到的所有进程
* 示例中nginx 进程由root 用户启动, 所以admin 用户无权杀死进程, 所以杀死进程失败

``` bash
[admin@localhost perl]$ pskill tomcat nginx
[1] root       2974      1  0 00:27 ?        00:00:00 nginx: master process nginx
[2] nginx      2975   2974  0 00:27 ?        00:00:00 nginx: worker process
[3] admin      3629      1 59 01:31 pts/1    00:00:07 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-7-7080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xms516m -Xmx1024m -Xss1024K -XX:PermSize=512m -XX:MaxPermSize=512m -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-7-7080/endorsed -classpath /opt/app/tomcat/tomcat-7-7080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-7-7080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-7-7080 -Dcatalina.home=/opt/app/tomcat/tomcat-7-7080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-7-7080/temp org.apache.catalina.startup.Bootstrap start
[4] admin      3647      1 47 01:31 pts/1    00:00:04 /opt/app/jdk/jdk1.7.0_80/bin/java -Djava.util.logging.config.file=/opt/app/tomcat/tomcat-8-8080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Djava.endorsed.dirs=/opt/app/tomcat/tomcat-8-8080/endorsed -classpath /opt/app/tomcat/tomcat-8-8080/bin/bootstrap.jar:/opt/app/tomcat/tomcat-8-8080/bin/tomcat-juli.jar -Dcatalina.base=/opt/app/tomcat/tomcat-8-8080 -Dcatalina.home=/opt/app/tomcat/tomcat-8-8080 -Djava.io.tmpdir=/opt/app/tomcat/tomcat-8-8080/temp org.apache.catalina.startup.Bootstrap start
[5] admin      3658   1656  0 01:31 pts/1    00:00:00 /usr/bin/perl ./pskill tomcat nginx

[输入] 请输入您要杀死的进程序号，0表示所有:  0
[失败] 进程pid:  2974 强制杀失败!
[失败] 进程pid:  2975 强制杀失败!
[成功] 进程pid:   3629 强制杀死成功!
[成功] 进程pid:   3647 强制杀死成功!
Killed
[admin@localhost perl]$ psgrep nginx
[1] root       2974      1  0 00:27 ?        00:00:00 nginx: master process nginx
[2] nginx      2975   2974  0 00:27 ?        00:00:00 nginx: worker process
[admin@localhost perl]$ 
```




