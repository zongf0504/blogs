# linux 工具 -- confview

> 通常情况下, linux 下的配置文件会有很多注释, 注释通常是由\#开头的. 当我们去查看配置文件内容时, 会被大量的注释干扰到, 不能对所有配置有一个整体的把控.因此我们需要一个能自动过滤空行和注释行, 并能对关键字高亮显示的工具. 因此, 笔者就写了一个专门看配置文件内容的工具.此工具虽然局限性有点儿大, 但确实非常好用的一个小工具.

## 1. 工具特色

* 自动过滤空行和以\#开头的注释行
* 高亮显示多个关键字
* 显示真实行号

## 2. confview 开发

### 2.1 源程序 confview.pl

```perl
#!/usr/bin/perl
#Desc  查看配置文件, 默认过滤#开头的注释行和空行, 可添加选项-a 不过滤或添加 -n 不显示行号
#Auth  zonggf
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
        &print_help("Desc","查看配置文件并高亮显示关键字, 默认过滤#开头的注释行和空行, 可添加选项-a 不过滤");
        &print_help("Args","-a 可选参数, 不过滤空号和注释号",
                        "-n 隐藏行号, 和-a 不能同时使用,只能使用一个",
                        "配置文件名称",
                        "筛选关键字,可同时输入多个关键字, 关键字直接用空格隔开, 多个关键字直接为或的关系");
        &print_help("Exam","confview /etc/udev/rules.d/70-persistent-net.rules eth0 add: 过滤空行,注释行,高亮显示eth0 add,显示行号",
                 "confview -a /etc/udev/rules.d/70-persistent-net.rules eth0 add: 不过滤空行,注释行,高亮显示关eth0 add,显示行号",
                 "confview -n /etc/udev/rules.d/70-persistent-net.rules eth0 add: 过滤空行,注释行,高亮显示关eth0 add,不显示行号" );
        &print_help("Auth","zongf");
        &print_help("Date","2017-07-15");

    exit;
  }elsif ("-a" eq $param){
    #显示所有, 不过滤空行和注释行
    $show_all = 1;
    shift @ARGV;
  }elsif ("-n" eq $param){
    #不显示行号
    $hide_number = 1;
    shift @ARGV;
  }
  # 检测是否有配置文件名 
  if( -1 == $#ARGV ){
      print BOLD RED "[error] "; print "请输入要查看的配置文件名\n"; 
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

######################################## 主程序  ########################################

#校验帮助
&check_help;

#配置文件名称
$file_name = shift @ARGV;

#读取文件内容，获取文件行数
open $f_cfg, "<", $file_name or die "[错误] $file_name not exists !!\n";
@lines = <$f_cfg>;
$lines_length = @lines;

#遍历文件内容
for $idx (1 .. @lines){
   $line = $lines[$idx];

   #如果是空行或者注释行
   if($line =~ /^$|^\s*#/){
      if(defined $show_all){
        print BOLD GREEN &fmt_idx($idx,$lines_length);
        &print_color($line ,@ARGV);
      }
   }else{
       #当show_numer 为1 时, 不输出行号
       print BOLD GREEN &fmt_idx($idx,$lines_length) unless $hide_number == 1;
       &print_color($line ,@ARGV);
       #记录总行数
       $rs_cnt++;
   }
}

#打印配置文件总行数
print BOLD CYAN "\nEffective Lines: $rs_cnt; Total Lines: $lines_length\n";

#关闭文件流
close $f_cfg;
```

### 2.2 加密为二进制程序

* 使用笔者自己开发的perl2bin 工具将源文件加密为二进制程序, 并删除源程序 
* 将二进制程序pskill 移动到某个环境变量目录, 笔者设置的环境变量目录为: /usr/local/bin/perl/

```bash
[admin@localhost perl]$ ls
confview.pl
[admin@localhost perl]$ perl2bin confview.pl 
开始转换脚本:confview.pl
[admin@localhost perl]$ mv confview /usr/local/bin/perl/
```

## 3. 测试

### 3.1 获取帮助信息

* 使用-h 或 --help 选项获取命令的帮助信息

```bash
[admin@localhost perl]$ ./confview.pl -h
Desc: 查看配置文件并高亮显示关键字, 默认过滤#开头的注释行和空行, 可添加选项-a 不过滤
Args: -a 可选参数, 不过滤空号和注释号
      -n 隐藏行号, 和-a 不能同时使用,只能使用一个
      配置文件名称
      筛选关键字,可同时输入多个关键字, 关键字直接用空格隔开, 多个关键字直接为或的关系
Exam: confview /etc/udev/rules.d/70-persistent-net.rules eth0 add: 过滤空行,注释行,高亮显示eth0 add,显示行号
      confview -a /etc/udev/rules.d/70-persistent-net.rules eth0 add: 不过滤空行,注释行,高亮显示关eth0 add,显示行号
      confview -n /etc/udev/rules.d/70-persistent-net.rules eth0 add: 过滤空行,注释行,高亮显示关eth0 add,不显示行号
Auth: zongf
Date: 2017-07-15
[admin@localhost perl]$ ./confview.pl --help
Desc: 查看配置文件并高亮显示关键字, 默认过滤#开头的注释行和空行, 可添加选项-a 不过滤
Args: -a 可选参数, 不过滤空号和注释号
      -n 隐藏行号, 和-a 不能同时使用,只能使用一个
      配置文件名称
      筛选关键字,可同时输入多个关键字, 关键字直接用空格隔开, 多个关键字直接为或的关系
Exam: confview /etc/udev/rules.d/70-persistent-net.rules eth0 add: 过滤空行,注释行,高亮显示eth0 add,显示行号
      confview -a /etc/udev/rules.d/70-persistent-net.rules eth0 add: 不过滤空行,注释行,高亮显示关eth0 add,显示行号
      confview -n /etc/udev/rules.d/70-persistent-net.rules eth0 add: 过滤空行,注释行,高亮显示关eth0 add,不显示行号
Auth: zongf
Date: 2017-07-15
```

### 3.2 显示配置文件全部内容, 并高亮显示

* 查看配置文件全部内容, 并显示行号, 高亮eth0 和 add 关键字 

```bash
[admin@localhost perl]$ confview -a /etc/udev/rules.d/70-persistent-net.rules eth0 add 
[1] # program, run by the persistent-net-generator.rules rules file.
[2] #
[3] # You can modify it, as long as you keep each rule on a single
[4] # line, and change only the value of the NAME= key.
[5] 
[6] # PCI device 0x8086:0x100f (e1000)
[7] SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:0c:29:5c:71:36", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"
[8] 
Effective Lines: 1; Total Lines: 8
```

### 3.3 显示配置文件内容, 过滤空行和注释

* 查看配置文件, 过滤空行和以\#开头的注释行,高亮关键字add 和 eth0, 显示行号

```bash
[admin@localhost perl]$ confview /etc/udev/rules.d/70-persistent-net.rules eth0 add   
[7] SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:0c:29:5c:71:36", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"

Effective Lines: 1; Total Lines: 8
```

### 3.4 显示配置文件内容, 过滤空行和注释, 但不显示行号

* 查看配置文件,过滤空行和以\#开头的注释行, 高亮关键字add 和 eth0, 不显示行号

```bash
[admin@localhost perl]$ confview -n /etc/udev/rules.d/70-persistent-net.rules eth0 add
SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="00:0c:29:5c:71:36", ATTR{type}=="1", KERNEL=="eth*", NAME="eth0"

Effective Lines: 1; Total Lines: 8
```



