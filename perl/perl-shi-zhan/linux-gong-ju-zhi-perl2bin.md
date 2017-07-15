#Linux 工具--perl2bin
> 在工作中，通常会写一些shell 脚本， perl 脚本工具，用于简化服务器使用。有时，脚本中会涉及一些用户名，密码等敏感信息，通常这些信息是不应该直接明文放到服务器脚本之中的，这样会有安全性问题。或者，我们要防止其他人有意无意的修改脚本。鉴于此类的需求，将脚本加密成二进制文件，是比较好的选择。虽然说也可以通过某种方法解密脚本，但是，想实现还是比较困难的。 脚本加密的方式有很多，笔者推荐使用shc 加密。 shc 并非是服务器自带的工具，需要自己下载安装。perl2bin 就是对shc的一个封装.


## 1. 源程序

```perl
#!/usr/bin/perl
#Desc 将脚本加密为二进制程序, 可进行批量转换
#Auth zongf
#Date 2016-12-22

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
    &print_help("Desc","加密脚本程序为二进制程序，默认生成不包含后缀名的二进制文件,保留源文件但收回源文件可执行权限,无过期时间",
                "注意:此脚本运行依赖于shc程序, 请确保已经安装了shc环境",);
    &print_help("Args","-d 选项可选，表示不保留源文件; 如果设置,则必须为第一个参数",
                "2017.01.12 选项可选，设置脚本有效期; 如果设置,则必须在脚本名称之前",
                "脚本列表, 可进行批量转换, 多个脚本直接用空格隔开, 脚本名称必须包含后缀名");
    &print_help("Exam","per2bin perl2bin.pl,生成perl2bin二进制文件, 有效期:永远,保留源文件",
                "perl2bin -d perl2bin.pl, 生成perl2bin二进制文件, 不限制有效期, 删除源文件perl2bin.pl",
                "perl2bin 2019.01.01 perl2bin.pl, 生成perl2bin 二进制文件, 使用期限截止为2019年1月1号, 保留源文件perl2bin.pl",
                "perl2bin -d 2019.01.01 perl2bin.pl, 生成perl2bin 二进制文件, 使用期限截止为2019年1月1号, 删除源文件perl2bin.pl",
                "perl2bin perl2bin.pl pskill.pl; 批量加密脚本, 生成perl2bin, pskill 二进制文件");
    &print_help("Auth","zongf");
    &print_help("Date","2017-07-15");
    exit;
  }elsif ("-d" eq $param) {
    $delete = 1;
    shift @ARGV;
  }
}


########################################   主程序    ########################################
#检查参数
&check_help;

#检查是否有过期时间限制
if($ARGV[0] =~ /\d{4}\.\d{2}\.\d{2}/){
  $expire_date = shift @ARGV;
}

#检查文件名是否合法:必须包含后缀名
foreach(@ARGV){
  unless(/^.+\..+$/){
    print "[错误] 脚本名格式不正确, $_ 不包含后缀名\n";
    exit();
  }
}

#基本命令
if(defined $expire_date){
  my $expire_tip = "The usage period of this command is $expire_date !";
  my ($y,$m,$d) = split(/\./,$expire_date);
  $basc_cmd = "shc -e $d\/$m\/$y -m '$expire_tip' -rTf ";
}else{
  $basc_cmd = 'shc -rTf ';
}

if(@ARGV <=0){
  print "请至少传入一个参数\n";
  exit;
}else{
  foreach(@ARGV){
    print "开始转换脚本:$_\n";
    #1. 生成name.x.c 和  name.x 文件
    system "$basc_cmd $_";
   
    #2. 删除name.x.c 文件
    system "rm -f $_.x.c";
   
    #3. 删除源文件或收回源文件可执行权限
    if(defined $delete){
        system "rm -f $_";
    }else{
        system "chmod a-x $_";
    }   

    #4.获取源文件的简单名称, 即最后一个. 之前的字符串
    $idx = rindex($_, '.');
    $simple_name = substr($_,0,$idx);

    #5. 修改源文件.x 文件名为源文件的简单名称
    system "mv $_.x $simple_name";

  }
}
```