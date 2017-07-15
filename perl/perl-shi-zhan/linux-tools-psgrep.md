# Linux 工具之 psgrep 
> 通常我们查看linux 运行的进程时会使用ps-ef | grep xxx 命令查看, 但是ps -ef | grep xxx 命令展示的效果并不太美观, 结果没有高亮显示, 没有索引.因此, 笔者开发了psgrep 工具, 

## 1. psgrep 特色
* 语法简单
* 输出美观,关键字高亮显示,有序号

## 2. psgrep 

### 2.1 源码psgrep.pl

```perl
#!/usr/bin/perl
#Desc  查看并筛选正在运行的进程, ps -ef | grep xxx
#Auth  zonggf
#Date  2017-07-15

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
    &print_help("Desc","查看当前正在运行的进程, 高亮关键字,序号标出",
                       "注意:此脚本运行依赖于shc程序, 请确保已经安装了shc环境",);
    &print_help("Args","筛选的字符串,可同时输入多个关键字, 关键字直接用空格隔开, 多个关键字直接为或的关系",);
    &print_help("Exam","psgrep tomcat: 查询正在运行的包含tomcat 的进程",
                       "psgrep tomcat nginx: 查询正在运行的包含tomcat 或nginx 的进程");
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
#参数: 接收两个参数: para1 需要格式化的索引  para2 数组长度
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

#进行参数拼接
@patterns = @ARGV;

$ports .= shift @ARGV;
foreach (@ARGV){
  $ports .="|$_";
}

#如果参数不为空，则进行筛选
$ps_cmd = 'ps -ef | grep -v /usr/bin/perl | grep -v "grep -E" ';
$ps_cmd .= " | grep -E \"" . $ports ."\"" if defined $ports;
@lines = `$ps_cmd`;

#遍历输出结果
foreach $idx (1 .. @lines){
    
    #打印序号
    print BOLD GREEN "[" . $idx . "] ";

    #打印内容
    &print_color($lines[$idx-1] ,@patterns);
}


```

### 2.2 加密为二进制程序
* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序
* 将二进制程序psgrep 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```perl
[admin@localhost perl]$ ls
psgrep.pl
[admin@localhost perl]$ perl2bin -d psgrep.pl 
开始转换脚本:psgrep.pl
[admin@localhost perl]$ ls
psgrep
[admin@localhost perl]$ mv psgrep /usr/local/bin/perl/
```

## 3. 测试

### 3.1 查看当前正在运行的nginx 进程
* 查看当前运行的nginx 进程
* 查看当前运行的tomcat 进程
![](/assets/perl_2017-07-15_100047.png)


### 3.2 查看当前正在运行的tomcat 和 nginx 进程
* 查看当前运行的tomcat 或 nginx 进程
![](/assets/perl_2017-07-15_100113.png)









