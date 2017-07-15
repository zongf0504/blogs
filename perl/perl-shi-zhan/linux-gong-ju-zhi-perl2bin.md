# Linux 工具--perl2bin
> 通常写的脚本都是文本文件, 如果脚本中有一些用户名密码等敏感信息,那么明文是很不安全的.因此,笔者通常会将包含敏感信息或不希望其他人修改的脚本加密为二进制文件,这样就加强了脚本的安全性.脚本加密地方式有很多,笔者推荐使用shc 加密. shc并非是服务器自带的工具, 需要自己安装. perl2bin 就是在shc的基础上进行的封装,目的是使加密脚本更简单.

## 1. 源程序

```perl
#!/usr/bin/perl
#Desc 将脚本加密为二进制程序, 可进行批量转换
#Auth zongf
#Date 2017-07-15

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

## 2 查看脚本帮助
* 可以通过选项-h,--help 查看帮助信息

```bash
[admin@localhost perl]$ ./perl2bin.pl -h
Desc: 加密脚本程序为二进制程序，默认生成不包含后缀名的二进制文件,保留源文件但收回源文件可执行权限,无过期时间
      注意:此脚本运行依赖于shc程序, 请确保已经安装了shc环境
Args: -d 选项可选，表示不保留源文件; 如果设置,则必须为第一个参数
      2017.01.12 选项可选，设置脚本有效期; 如果设置,则必须在脚本名称之前
      脚本列表, 可进行批量转换, 多个脚本直接用空格隔开, 脚本名称必须包含后缀名
Exam: per2bin perl2bin.pl,生成perl2bin二进制文件, 有效期:永远,保留源文件
      perl2bin -d perl2bin.pl, 生成perl2bin二进制文件, 不限制有效期, 删除源文件perl2bin.pl
      perl2bin 2019.01.01 perl2bin.pl, 生成perl2bin 二进制文件, 使用期限截止为2019年1月1号, 保留源文件perl2bin.pl
      perl2bin -d 2019.01.01 perl2bin.pl, 生成perl2bin 二进制文件, 使用期限截止为2019年1月1号, 删除源文件perl2bin.pl
      perl2bin perl2bin.pl pskill.pl; 批量加密脚本, 生成perl2bin, pskill 二进制文件
Auth: zongf
Date: 2017-07-15
[admin@localhost perl]$ ./perl2bin.pl --help
Desc: 加密脚本程序为二进制程序，默认生成不包含后缀名的二进制文件,保留源文件但收回源文件可执行权限,无过期时间
      注意:此脚本运行依赖于shc程序, 请确保已经安装了shc环境
Args: -d 选项可选，表示不保留源文件; 如果设置,则必须为第一个参数
      2017.01.12 选项可选，设置脚本有效期; 如果设置,则必须在脚本名称之前
      脚本列表, 可进行批量转换, 多个脚本直接用空格隔开, 脚本名称必须包含后缀名
Exam: per2bin perl2bin.pl,生成perl2bin二进制文件, 有效期:永远,保留源文件
      perl2bin -d perl2bin.pl, 生成perl2bin二进制文件, 不限制有效期, 删除源文件perl2bin.pl
      perl2bin 2019.01.01 perl2bin.pl, 生成perl2bin 二进制文件, 使用期限截止为2019年1月1号, 保留源文件perl2bin.pl
      perl2bin -d 2019.01.01 perl2bin.pl, 生成perl2bin 二进制文件, 使用期限截止为2019年1月1号, 删除源文件perl2bin.pl
      perl2bin perl2bin.pl pskill.pl; 批量加密脚本, 生成perl2bin, pskill 二进制文件
Auth: zongf
Date: 2017-07-15
```

## 3. 测试

编写两个最简单的perl 脚本hello.pl 和 world.pl:

```perl
#!/usr/bin/perl
print "hello,world\n";
```

### 3.1 默认批量转换
* 批量转换, 全部使用默认设置
* 转换前:源文件hello.pl, world.pl 都拥有可执行权限
* 转换后:保留源文件, 但收回源文件的可执行权限
* 转换后:生成新的二进制文件,hello和world, 移除了后缀名, 拥有可执行权限

```bash
[admin@localhost perl]$ ll ./*
-rwxrwxr-x. 1 admin admin   40 Jan  2 00:03 ./hello.pl
-rwxr-xr-x. 1 admin admin 3440 Jul 15 16:57 ./perl2bin.pl
-rwxrwxr-x. 1 admin admin   40 Jan  2 00:04 ./world.pl
[admin@localhost perl]$ ./perl2bin.pl hello.pl world.pl 
开始转换脚本:hello.pl
开始转换脚本:world.pl
[admin@localhost perl]$ ll
total 36
-rwx-wx--x. 1 admin admin 9312 Jan  2 00:07 hello
-rw-rw-r--. 1 admin admin   40 Jan  2 00:03 hello.pl
-rwxr-xr-x. 1 admin admin 3440 Jul 15 16:57 perl2bin.pl
-rwx-wx--x. 1 admin admin 9312 Jan  2 00:07 world
-rw-rw-r--. 1 admin admin   40 Jan  2 00:04 world.pl
[admin@localhost perl]$ ./hello 
hello,world
[admin@localhost perl]$ ./world 
hello,world
```

### 3.2 不保留源文件
* 转换时,使用-d 参数, 将不会保留源文件
* 除了删除了源文件, 转换效果是一样的
```perl
[admin@localhost perl]$ ./perl2bin.pl -d hello.pl world.pl 
开始转换脚本:hello.pl
开始转换脚本:world.pl
[admin@localhost perl]$ ls
hello  perl2bin.pl  world
```

### 3.3 设置过期时间
* 日期之前格式为: yyyy.MM.dd , 日期必须放置在脚本名称前
* 如果使用-d参数的话,那么日期必须在-d之后

```perl
[admin@localhost perl]$ ls
hello.pl  perl2bin.pl  world.pl
[admin@localhost perl]$ date
Sat Jul 15 00:00:55 CST 2017
[admin@localhost perl]$ ./perl2bin.pl -d 2017.01.01 hello.pl world.pl 
开始转换脚本:hello.pl
开始转换脚本:world.pl
[admin@localhost perl]$ ls
hello  perl2bin.pl  world
[admin@localhost perl]$ ./hello 
./hello: has expired!
The usage period of this command is 2017.01.01 !
[admin@localhost perl]$ ./world 
./world: has expired!
The usage period of this command is 2017.01.01 !
```

### 3.4 加密本身
* perl2bin.pl 脚本不仅可以加密其它脚本,还可以加密本身.加密之后然后放到环境变量目录中, 那么它就从一个普通脚本升级为一个linux工具了,可以使用perl2bin加密其它脚本.

```perl
[admin@localhost perl]$ ls
hello.pl  perl2bin.pl  world.pl
[admin@localhost perl]$ ./perl2bin.pl perl2bin.pl 
开始转换脚本:perl2bin.pl
[admin@localhost perl]$ ls
hello.pl  perl2bin  perl2bin.pl  world.pl
[admin@localhost perl]$ ./perl2bin hello.pl 
开始转换脚本:hello.pl
[admin@localhost perl]$ ls
hello  hello.pl  perl2bin  perl2bin.pl  world.pl
[admin@localhost perl]$ ./hello 
hello,world
```

## 附:shc安装
1. 下载shc源文件: [shc-3.8.9.tgz](http://download.csdn.net/detail/zgf19930504/9724681)
2. 解压文件: tar -zxvf shc-3.8.9.tgz
3. 编译安装: make && make install
4. 遇到: Do you want continue? 时输入y即可完成安装












