# Perl 数据类型--数组变量

> Perl 语言中提供了数组变量, 用于标识一组元素的集合. Perl 的数组功能是很强大的, 提供了很多好用的API. 数组元素代表多个数据, 为复数, 所以变量名用@修饰.

# 1. 数组特性

* perl 数组元素类型可以是字符串, 也可以是数字
* perl 数组可以当做栈使用
* perl 数组可以当做队列使用
* perl 数组支持反向索引
* perl 数组可以不定义直接使用, 在使用的同时自动创建

# 2. 数组基本操作

## 2.1 创建数组

* 数组变量名: @+变量名, 如:@abooks, @students
* perl 数组可在定义的时候, 直接使用列表直接量赋值 
* 使用数组元素赋值时, 如果数组未定义,那么会自动创建一个新的全局数组.

| 创建方式 | 示例 |
| :--- | :--- |
| 创建空数组 | @null = \(\); |
| 创建数字数组 | @ints = \( 1, 2, 3, 4\); @ints = \( 1..4 \); |
| 复制数组 | @ints2 = @ints; |
| 创建混合数组 | @array = \(@ints, 1..3, $name \); |
| 数组元素赋值 | $array\[0\]="java" |

## 2.2 数组引用

* 数组元素引用格式: $array\[index\], array 为数组名称, index 为索引号
* 数组元素为单数, 用$符号引用; 数组本身为复数, 使用@引用.
* 数组正向索引下标从0 开始, 0 表示从前向后第一个元素
* 数组反向索引下标从-1 开始, 表示从后向前第一个元素

| 数组引用 | 示例 | 描述 |
| :--- | :--- | :--- |
| 元素引用 | $books\[0\], $books\[1\] | 引用数组中单一元素 |
| 整体引用 | @books, @ints | 引用数组整体 |


## 2.3 数组元素提取

使用@符合可以提取数组中的多个元素组成新的数组或为标量批量赋值:

* 提取为数组: @array=@books\[0,3\];
* 批量赋值: \($book0, $book3\) = @books\[0,3\];

## 2.4. 字符串内插数组

* 字符串中可直接内插数组或数组元素, 这是perl 的一个非常方便的功能. 常在开发测试脚本过程中使用.

| 数组内插 | 示例 | 描述 |
| :--- | :--- | :--- |
| 内插元素 | "$books\[0\]", "$books\[1\]" | 和普通标量内插无异 |
| 内插数组 | @books, @ints | 将所用元素依次用空格隔开,组成一个字符串 |

## 2.5 数组上下文

* perl 的数组是非常智能的, 能够揣摩你的代码上下文, 给你不同的返回
* 上下文是指,根据左侧变量类型,使用数组做不同的赋值.

| 数组上下文 | 示例 | 描述 |
| :--- | :--- | :--- |
| 数组上下文 | @array\_1 = @array\_name; | 等号左侧为数组, 因此此语句表示复制数组 |
| 标量上下文 | $length = @array\_name | 等号左侧为标量, 标量表示单数, 所以此表达式为获取数组长度 |
| 哈希上下文 | %hash = @array\_name | 等号左侧为哈希变量, 则表示将数组转换为哈希结构 |

# 3. 数组遍历

* perl 语言中对数组的遍历方式很多, 使用起来及其方便.
* 不同的遍历方式适用不同的场景, 笔者建议全部掌握这五种遍历方式

## 3.1 字符串内插法遍历

* 字符串内插法遍历是最简单的遍历方法, $\# 表示数组的最大索引值, 为数组长度-1
* 数组元素之间用空格隔开, 当数组元素本身就有空格时, 展示有有点问题了.
* 通常只用于测试阶段对数组做简单输出, 方便快捷

```perl
@books = ("java", "mysql", "linux", "perl");
print "books--maxIdx: $#books, elements:@books\n";
```

## 3.2 foreach 单行遍历

* 使用foreach 和内置变量$_ 遍历, $_ 表示数组中的一个元素
* 通常用于遍历输出, 数组元素转字符串等操作.

```perl
print "$_\n" foreach @books;
```

## 3.3 foreach-默认变量遍历

* 使用foreach + 内置变量方式遍历数组, 每次遍历中可做一些业务处理
* 只能获取数组元素内容, 不能获取数组元素与索引的对应关系

```perl
foreach (@books){
    print "$_\n";
}
```

## 3.4 for 循环遍历

* 普通的for 循环遍历, $\# 表示获取到数组索引最大值
* 能获取数组元素与索引的对应关系

```perl
for my $idx (0..$#books){
    print "$idx  -->  $bookArray[$idx]\n";
}
```

## 3.5 while-each 列表遍历法

* while-each 直接把索引值和数组元素组成一个key-value 的列表
* 能获取数组元素与索引的对应关系

```perl
while(my ($idx, $val) = each @bookArray){
    print "$idx --> $val\n";
}
```

# 4. 数组常用方法

## 4.1 基本用法

| 方法 | 描述 | 示例 |
| :--- | :--- | :--- |
| push | 从数组尾部压入一个或多个元素, 返回新数组元素个数 | $length=push\(@books, 'es'\); $length= push\(@books, @ints\); |
| pop | 从数组尾部弹出\(删除\)一个元素, 返回删除的元素 | $el = pop @books; |
| unshift | 从数组头部压入一个或多个元素, 返回新数组元素个数 | $length=unshift\(@books, 'es'\); $length= unshift\(@books, @ints\); |
| shift | 从数组头部弹出\(删除\)一个元素, 返回删除的袁术 | $el = shift @books; |
| reverse | 反转数组, 返回反转之后的数组,不会修改原数组 | @books2 = reverse @books; |
| sort | 对数组按字符串升序排列, 返回排序后的数组, 不会修改原数组 | @books3 = sort @books; |
| splice | 对任意数组元素进行删除,替换,插入操作, 会修改原数组 | 语法格式:splice\(目标数组， 开始数组下标， 删除个数， 替换数组\) |

## 4.2 高级用法

### 4.2.1 数组元素提取grep

* grep 可以从数组中提取符合规则的元素组成新的数组
* grep 不会修改原数组的内容
* grep 通常和正则表达式结合 使用
* grep 可接单行多行操作
* grep 不会对原数组产生影响

| 用法示例 | 描述 |
| :--- | :--- |
| @jaArray = grep /ja/, @books; | 提取books 数组中所有包含ja的元素,组成新的数组 |
| @JAArray = grep s/ja/JA/, @books; | 提取books 数组中所有包含ja的元素, 并将ja替换为JA后组成新 数组 |
| @JAArray = grep { /ja/; print "$\_";} @books; | 提取books 中所有包含ja的元素, 遍历过程中输出每一个元素,\(笔者目前并没有发现适合此种用法的场景\) |

### 4.2.2 splice 删除,替换数组元素

* 使用splice 可以进行删除替换数组元素, 语法: splice\(目标数组，开始删除的索引， 删除个数， 替换数组\)
* 数组索引: 要删除开始的索引, 包含此索引元素
* 批量操作时,只能是连续的批量操作, 不能是间隔的批量操作.
* splice 直接对元素组进行操作, 每一个操作都会影响原来的数组.

| 语法 | 描述 |
| :--- | :--- |
| splice\(@array, $idx\); | 批量连续删除:删除索引idx及以后的元素, 返回删除的所有元素 |
| splice\(@array, $idx, n\); | 批量连续删除:删除从索引idx开始的n个元素, 返回删除的所有元素 |
| splice\(@array, $idx, 1\); | 单个删除:删除索引为idx 的元素, 返回删除的一个元素 |
| splice\(@array, $idx, 0, @array\); | 插入: 索引idx 后插入数组或列表 |
| splice\(@array, $idx, 1, $el\); | 单个替换: 替换索引为idx 的元素为el |
| splice\(@array, $idx, n, @array\); | 批量连续替换: 依次替换从索引idx开始的n个元素为数组内容 |

## 4.3 工具类方法

* perl 自带了List::Util 模块, 封装了一些常用的工具类方法, 但是需要引入List::Util 模块儿.
* 引入方式: use List::Util;

常用工具方法:

| 方法 | 描述 | 示例 |
| :--- | :--- | :--- |
| min | 对数组元素按数值型直接量计算最小值, 返回最小值 | $min = List::Util::min\(@ints\); |
| max | 对数组元素按数值型直接量计算最大值, 返回最大值 | $max = List::Util::max\(@ints\); |
| sum | 对数组元素按数值型直接量求和, 返回数组元素和 | $sum = List::Util::sum\(@ints\); |
| minstr | 对数组元素按字符串直接量计算最小值, 返回最小值 | $minstr = List::Util::minstr\(@books\); |
| maxstr | 对数组元素按字符串直接量计算最大值, 返回最大值 | $maxstr = List::Util::maxstr\(@books\); |
| shuffle | 对数组随机排序, 返回排序后的数组, 不影响原数组 | @ints\_random = List::Util::shuffle\(@ints\); |

# 5. 测试用例
## 5.1 测试脚本
```perl
#!/usr/bin/perl
#Desc 数组测试demo

use List::Util;

print "\n####################  2.1 创建数组  ####################\n";
#创建空数组:
@null=();
print "null: @null \n";

#创建数字数组
@ints = ( 0, 1, 2, 3..8, 9);
print "ints: @ints \n";

#复制创建数组
@ints2 = @ints;
print "ints2: @ints2 \n";

#创建混合数组
@arrays = ( a, b, @ints, "java");
print "arrays:@arrays \n";

#自动创建
$ints[1] = "java";
print "ints: @ints \n";

print "\n####################  2.2 数组引用  ####################\n";
#单个元素引用
$int0 = $ints[0];
print "int[0]:$ints[0]\n";

#数组整体引用
@ints3 = @ints;
print "ints3: @ints \n";

print "\n####################  2.3 数组元素提取  ####################\n";
@int_3_7 = @ints[3, 7];
print "ints[3]-ints[7]: @int_3_7 \n";


print "\n####################  2.4 字符串内插  ####################\n";
#数组内插
print "ints: @ints \n";

#数组元素内插
print "int[0]: $ints[0] \n";

print "\n####################  2.5 数组上下文  ####################\n";
#标量上下文
$length = @ints;
print "ints length: $length \n";

#数组上下文:
@ints4 = @ints;
print "ints4: @ints4 \n";

#哈希上下文:
@chars = ( A, 'a', B, 'b', C, 'c');
%char_hs = @chars;

@char_keys = keys %char_hs;
@char_vals = values %char_hs;
print "keys: @char_keys, values: @char_vals \n";

@ints = ( 0 ..9 );
print "\n####################  3.1 字符串内插法遍历  ####################\n";
print "ints: @ints";

print "\n####################  3.2 foreach 单行遍历  ####################\n";
print "$_\n" foreach @ints;

print "\n####################  3.3 foreach 默认变量遍历  ####################\n";
foreach (@ints){
  print "$_\n";
}

print "\n####################  3.4 for 循环遍历  ####################\n";
for my $idx (0..$#ints){
  print "ints[$idx]=$ints[$idx]\n";
}

print "\n####################  3.5 while-each 列表遍历  ####################\n";
# 需要5.12+版本才行
#while(my ($idx, $val) = each @ints ) {
#   print "ints[$idx]=$val \n";
#}

print "\n####################  4.1 数组常用方法  ####################\n";
print "push: 向ints 尾部添加一个元素a \n";
@ints = (0 .. 9);
$length = push(@ints, a);
print "length:$length, ints: @ints \n";


print "pop: 向ints 尾部弹出(删除)一个元素 \n";
@ints = (0 .. 9);
$del = pop @ints ;
print "del:$del, ints: @ints \n";

print "push: 向ints 头部添加一个元素a \n";
@ints = (0 .. 9);
$length = unshift(@ints, a);
print "length:$length, ints: @ints \n";

print "push: 向ints 头部弹出(删除)一个元素 \n";
@ints = (0 .. 9);
$del = shift @ints;
print "del:$del, ints: @ints \n";

print "reverse: 反转数组: \n";
@ints = (0 ..9);
@r_ints = reverse @ints;
print "ints: @ints, r_ints: @r_ints\n";

print "sort: 按字符串排序: \n";
@ints = (0..9);
@s_ints = sort @ints;
print "ints:@ints, s_ints: @s_ints\n";


print "\n####################  4.2.1 高级用法:grep 过滤数组  ####################\n";
@books = ("java", "javaScript" , "spring", "spring-mvc");
print "books: @books \n";

@javas = grep /java/, @books;
print "javas: @javas, books: @books \n";

@JAVAs = grep s/java/JAVA/g, @books;
print "JAVA: @JAVAs, books: @books \n";

@javas = grep { /java/; print "$_"; } @books;


print "\n####################  4.2.2 高级用法:splice 删除, 插入,替换元素  ####################\n";
@ints = ( 0..9);
print "ints; @ints \n";

print "连续删除: 删除索引为5及之后的索引元素:\n";
@ints = (0..9);
@dels = splice(@ints, 5);
print "ints: @ints, delete:@dels \n";

print "连续删除: 删除从索引5开始的3个元素, 即索引5, 6, 7:\n";
@ints = (0..9);
@dels = splice(@ints, 5, 3);
print "ints: @ints, delete: @dels \n";

print "单个删除: 删除索引5元素:\n";
@ints = (0..9);
$del = splice(@ints, 5, 1);
print "ints: @ints, delete: $del \n";

#插入元素
print "插入元素: 索引5后面插入3个元素:\n";
@ints = (0..9);
splice(@ints, 5, 0, (a, b, c) );
print "ints: @ints \n";

#替换单个元素
print "替换单个元素: 将索引5的元素替换为a: \n";
@ints = (0..9);
splice(@ints, 5, 1, a);
print "ints: @ints \n";

#替换连续元素
print "替换连续元素: 将从索引5开始的2个元素依次替换为a,b: \n";
@ints = (0..9);
splice(@ints, 5, 2, (a, b));
print "ints: @ints \n";


print "\n####################  4.2.3 工具类常用方法  ####################\n";
@ints = (0..10);
print "按数字直接量计算最大值, 最小值, 求和:@ints \n";
$min = List::Util::min(@ints);
$max = List::Util::max(@ints);
$sum = List::Util::sum(@ints);
print "min:$min, max:$max, sum:$sum \n";

@books = ("java", "mysql", "linux", "elastic");
print "按字符串直接量计算最大值, 最小值: @books \n";
$minstr = List::Util::minstr(@books);
$maxstr = List::Util::maxstr(@books);
print "minstr:$minstr, maxstr:$maxstr \n";

print "随机排列数组: @ints \n";
@s_ints = List::Util::shuffle(@ints);
print "ints  : @ints \n";
print "s_ints: @s_ints \n";
```

## 5.2 脚本输出
```bash
[admin@localhost perl]$ ./array.pl 

####################  2.1 创建数组  ####################
null:  
ints: 0 1 2 3 4 5 6 7 8 9 
ints2: 0 1 2 3 4 5 6 7 8 9 
arrays:a b 0 1 2 3 4 5 6 7 8 9 java 
ints: 0 java 2 3 4 5 6 7 8 9 

####################  2.2 数组引用  ####################
int[0]:0
ints3: 0 java 2 3 4 5 6 7 8 9 

####################  2.3 数组元素提取  ####################
ints[3]-ints[7]: 3 7 

####################  2.4 字符串内插  ####################
ints: 0 java 2 3 4 5 6 7 8 9 
int[0]: 0 

####################  2.5 数组上下文  ####################
ints length: 10 
ints4: 0 java 2 3 4 5 6 7 8 9 
keys: A C B, values: a c b 

####################  3.1 字符串内插法遍历  ####################
ints: 0 1 2 3 4 5 6 7 8 9
####################  3.2 foreach 单行遍历  ####################
0
1
2
3
4
5
6
7
8
9

####################  3.3 foreach 默认变量遍历  ####################
0
1
2
3
4
5
6
7
8
9

####################  3.4 for 循环遍历  ####################
ints[0]=0
ints[1]=1
ints[2]=2
ints[3]=3
ints[4]=4
ints[5]=5
ints[6]=6
ints[7]=7
ints[8]=8
ints[9]=9

####################  3.5 while-each 列表遍历  ####################

####################  4.1 数组常用方法  ####################
push: 向ints 尾部添加一个元素a 
length:11, ints: 0 1 2 3 4 5 6 7 8 9 a 
pop: 向ints 尾部弹出(删除)一个元素 
del:9, ints: 0 1 2 3 4 5 6 7 8 
push: 向ints 头部添加一个元素a 
length:11, ints: a 0 1 2 3 4 5 6 7 8 9 
push: 向ints 头部弹出(删除)一个元素 
del:0, ints: 1 2 3 4 5 6 7 8 9 
reverse: 反转数组: 
ints: 0 1 2 3 4 5 6 7 8 9, r_ints: 9 8 7 6 5 4 3 2 1 0
sort: 按字符串排序: 
ints:0 1 2 3 4 5 6 7 8 9, s_ints: 0 1 2 3 4 5 6 7 8 9

####################  4.2.1 高级用法:grep 过滤数组  ####################
books: java javaScript spring spring-mvc 
javas: java javaScript, books: java javaScript spring spring-mvc 
JAVA: JAVA JAVAScript, books: JAVA JAVAScript spring spring-mvc 
JAVAJAVAScriptspringspring-mvc
####################  4.2.2 高级用法:splice 删除, 插入,替换元素  ####################
ints; 0 1 2 3 4 5 6 7 8 9 
连续删除: 删除索引为5及之后的索引元素:
ints: 0 1 2 3 4, delete:5 6 7 8 9 
连续删除: 删除从索引5开始的3个元素, 即索引5, 6, 7:
ints: 0 1 2 3 4 8 9, delete: 5 6 7 
单个删除: 删除索引5元素:
ints: 0 1 2 3 4 6 7 8 9, delete: 5 
插入元素: 索引5后面插入3个元素:
ints: 0 1 2 3 4 a b c 5 6 7 8 9 
替换单个元素: 将索引5的元素替换为a: 
ints: 0 1 2 3 4 a 6 7 8 9 
替换连续元素: 将从索引5开始的2个元素依次替换为a,b: 
ints: 0 1 2 3 4 a b 7 8 9 

####################  4.2.3 工具类常用方法  ####################
按数字直接量计算最大值, 最小值, 求和:0 1 2 3 4 5 6 7 8 9 10 
min:0, max:10, sum:55 
按字符串直接量计算最大值, 最小值: java mysql linux elastic 
minstr:elastic, maxstr:mysql 
随机排列数组: 0 1 2 3 4 5 6 7 8 9 10 
ints  : 0 1 2 3 4 5 6 7 8 9 10 
s_ints: 9 3 2 10 6 0 7 4 1 5 8 

```


