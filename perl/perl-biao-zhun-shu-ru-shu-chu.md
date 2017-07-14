# Perl 标准输入输出
> 默认情况下, Perl 程序标准输入为键盘输入, 标准输出为终端显示器.

#1. 标准输入输出
## 1. 标准输入
* 使用<STDIN> 可以获取用户从键盘输入的一行信息, Enter 键结束输入, 输入结果包含换行符
* 通常会使用chomp函数去掉行尾的换行符, 使用标量接收键盘输入, 然后再做后续处理

```perl

#常见用法
print "please input :";
chomp($line=<STDIN>);
print "your input is : $line\n";

```

## 1.2 标准输出
* perl 程序的标准输出为用户终端显示器,可以使用print, printf, say来输出

### 1.2.1 print 
* print 输出一行字符串, 末尾不添加换行符, 如果需要换行末尾需要添加\n
* print 后跟双引号, 可以实现变量内插, 转义字符, 跟单引号则不会进行转换

```perl
print "hello\nworld\n";#输出两行,一行hello, 一行world
print 'hello\nworld\n';#输出一行: hello\nworld\n

#常见用法
$name="zong";
@books=("java", "mysql");
print "name:$name, books:@books\n"; #输出:name:zong, books:java mysql

```

### 1.2.2 printf 
* printf 是格式化输出字符串,类似于shell 脚本, c语言的printf, 只不过perl语言支持变量内插, 所以printf 就显得没有那么重要了
* printf 通常用于格式化串宽度, 对齐方式, 浮点型数字四舍五入等
* 如果输出%号, 需要使用%%
* 语法: printf "包含占位符的字符串表达式", 占位符参数...;

常用占位符:
| 占位符 | 含义 |
| :--- | :--- |
| %ns / %-ns | 字符串占位符, 宽度最少为n位, 对齐方式: 默认向右, 负号向左 |
| %nd / %-nd | 整数占位符,  宽度最少为n位, 对齐方式: 默认向右, 负号向左 |
| %n.mf / %-m.nf| 浮点型占位符, 整数宽度最少n位, 小数部分最多m位, 进行四舍五入, 对齐方式: 默认向右, 负号向左 |

```perl
#输出九九乘法表
for($i=1; $i<10; $i++){
  for($j=1; $j<$i; $j++){
     printf "%-4s%-2d  ", "$j*$i=", $i*$j;
  }
  print "\n";
}

printf "%4s, %2s, %20s\n", "姓名", "年龄", "成绩";
printf "%4s, %2d, %4.1f\n", "张小三", "20", "540.25";
printf "%4s, %2d, %4.1f\n", "李四", "18", "606.55";
```

## 1.2.3 say
* say 和print 比较类似, 也是输出一行字符串, 不同的是末尾会自动添加换行符, 无须手动添加\n
* 双引号内科进行变量内插和转义字符转换, 单引号内不支持, 输出的是原字符串
* say 语句不是自带的, 需要引入模块: use feature qw(say);

```perl
#!/usr/bin/perl
#
use feature qw(say);
$name="zong";
@books=("java", "mysql");
say"name:$name, books:@books"; #输出:name:zong, books:java mysql
```

# 3. 颜色输出
* 在写linux脚本时, 我们可能会想让某些输出进行高亮显示, perl 是支持颜色输出的, 但是引入ANSIColor 模块儿
* 颜色输出可以用于print 和 printf 语句

## 3.1 perl 语言支持的颜色输出

```perl
#!/usr/bin/perl

use warnings;

#引入颜色模块儿
use Term::ANSIColor qw(:constants);
$Term::ANSIColor::AUTORESET = 1;


printf "\n\n  perl 支持的颜色类型  \n\n";
printf BOLD  "BOLD\n";
printf CLEAR "CLEAR\n";
printf DARK "DARK\n";
printf UNDERLINE "UNDERLINE\n";
printf UNDERSCORE "UNDERSCOPRE\n";
printf BLINK "BLINK\n";
printf REVERSE "REVERSE\n";
printf CONCEALED "CONCELED\n";
printf BLACK "BLACK\n";
printf RED "RED\n";
printf GREEN "GREEN\n";
printf YELLOW "YELLOW\n";
printf BLUE "BLUE\n";
printf MAGENTA "MAGENTA\n";
printf CYAN "CYAN\n";
printf WHITE "WHITE\n";
printf ON_BLACK "ON_BLACK\n";
printf ON_RED "ON_RED\n";
printf ON_GREEN "ON_GREEN\n";
printf ON_YELLOW "ON_YELLOW\n";
printf ON_BLUE "ON_BLUE\n";
printf ON_MAGENTA "ON_MAGENTA\n";
printf ON_CYAN "ON_CYAN\n";
printf ON_WHITE "ON_WHITE\n";
```

## 3.2 最佳实践
* 通常会将一个字符串中的默写字符进行高亮显示, 而不会将整个字符串进行高亮显示, 所以封装一个颜色输出的方法. 

```perl
#!/usr/bin/perl

#只能在linux 中运行
#打印颜色必须引入的模块和设置
use Term::ANSIColor qw(:constants);
$Term::ANSIColor::AUTORESET = 1;

&print_color("hello,world,hello,java,hello,js\n","java","hell");


#desc    输出有颜色的字符串
#para1  接收至少两个参数以上，第一个参数为要格式化的字符串，之后的参数为要使用颜色的字符串
sub print_color(){
  die "print_color 方法需要至少两个参数" if @_ <2;
  #获取要格式化颜色的字符串
  my $line = shift @_;

  #获取要高亮的字符串数组
  @patterns = @_;

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
      if("@patterns" =~ $item){
        print BOLD RED $item;
      } else {
        print $item;
      }
  }
}
```








