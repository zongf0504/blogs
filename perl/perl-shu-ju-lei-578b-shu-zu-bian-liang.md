# Perl 数据类型--数组变量

> Perl 语言中提供了数组变量, 用于标识一组元素的集合. Perl 的数组功能是很强大的, 提供了很多好用的API.

# 1. 数组特点

* perl 数组元素类型可以是字符串, 也可以是数字
* perl 数组可以当做栈使用
* perl 数组可以当做队列使用

# 2. 数组基本操作

## 2.1 数组定义

* perl 数组变量使用@+变量名定义, 如:@abooks, @students
* perl 数组可在定义的时候, 直接使用列表直接量赋值 

## 2.2 数组赋值

| 赋值方式 | 示例 |
| :--- | :--- |
| 创建空数组 | @null = (); |
| 创建数字数组 | @ints = ( 1, 2, 3, 4); @ints = ( 1..4 ); |
| 复制数组 | @ints2 = @ints; |
| 创建混合数组 | @array = (@ints, 1..3, $name |

## 2.3 数组引用
* 数组元素引用格式: $array[index], array 为数组名称, index 为索引号
* 数组索引下标从0 开始
* 数组元素为单数, 用$符号引用; 数组本身为复数, 使用@引用.

| 数组引用 | 示例 |
| :--- | :--- |
| 元素引用 | $books[0], $books[1] |
| 整体引用 | @books, @ints|

## 2.4. 字符串内插数组
* 字符串中可直接内插数组或数组元素, 这是perl 的一个非常方便的功能. 常在开发测试脚本过程中使用.

| 数组内插 | 示例 | 描述 |
| :--- | :--- | :--- |
| 内插元素 | "$books[0]", "$books[1]" | 和普通标量内插无异 |
| 内插数组 | @books, @ints| 将所用元素依次用空格隔开,组成一个字符串 |

## 2.5 数组上下文
* perl 的数组是非常智能的, 能够揣摩你的代码上下文, 给你不同的返回

| 数组上下文 | 示例 | 描述 |
| :--- | :--- | :--- |
| 数组上下文 | @books1 = @books; | 等号左侧为数组, 因此此语句表示复制数组 |
| 标量上下文 | $length = @books | 等号左侧为标量, 标量表示单数, 所以此表达式为获取数组长度 |


# 3. 数组遍历
* perl 语言中对数组的遍历方式很多, 使用起来及其方便.
* 不同的遍历方式适用不同的场景, 笔者建议全部掌握这五种遍历方式

## 3.1 字符串内插法遍历
* 字符串内插法遍历是最简单的遍历方法, $# 表示数组的最大索引值, 为数组长度-1
* 数组元素之间用空格隔开, 当数组元素本身就有空格时, 展示有有点问题了.
* 通常只用于测试阶段对数组做简单输出, 方便快捷

```perl
@books = ("java", "mysql", "linux", "perl");
print "books--maxIdx: $#books, elements:@books\n";
```

## 3.2 foreach 单行遍历
* 使用foreach 和内置变量$_ 遍历, $_ 表示数组中的一个元素
* 通常用于遍历输出, 数组元素转字符串等操作.
``` perl
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
* 普通的for 循环遍历, $# 表示获取到数组索引最大值
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
| push | 从数组尾部压入一个或多个元素, 返回新数组元素个数 | $length=push(@books, 'es'); $length= push(@books, @ints); | 
| pop | 从数组尾部弹出(删除)一个元素, 返回删除的元素 | $el = pop @books; | 
| unshift | 从数组头部压入一个或多个元素, 返回新数组元素个数 | $length=unshift(@books, 'es'); $length= unshift(@books, @ints); | 
| shift | 从数组头部弹出(删除)一个元素, 返回删除的袁术 | $el = shift @books; | 
| reverse | 反转数组, 返回反转之后的数组,不会修改原数组 | @books2 = reverse @books;  | 
| sort | 对数组按字符串升序排列, 返回排序后的数组, 不会修改原数组 | @books3 = sort @books; | 
| splice | 对任意数组元素进行删除,替换,插入操作, 会修改原数组 | 语法格式:splice(目标数组， 开始数组下标， 删除个数， 替换数组)  | 

## 4.2 高级用法

### 4.2.1 数组元素提取grep
* grep 可以从数组中提取符合规则的元素组成新的数组
* grep 不会修改原数组的内容
* grep 通常和正则表达式结合 使用
* grep 可接单行多行操作

| 用法示例 | 描述 |
| :--- | :--- |
| @jaArray = grep /ja/, @books; | 提取books 数组中所有包含ja的元素,组成新的数组 |
| @JAArray = grep s/ja/JA/, @books;| 提取books 数组中所有包含ja的元素, 并将ja替换为JA后组成新 数组 |
| @JAArray = grep { /ja/; print "$_";} @books; | 提取books 中所有包含ja的元素, 遍历过程中输出每一个元素,(笔者目前并没有发现适合此种用法的场景) |



## 4.3 工具类方法
* perl 自带了List::Util 模块, 封装了一些常用的工具类方法, 但是需要引入List::Util 模块儿.
* 引入方式: use List::Util;

常用工具方法:
| 方法 | 描述 | 示例 |
| :--- | :--- | :--- |
| min | 对数组元素按数值型直接量计算最小值, 返回最小值 | $min = List::Util::min(@ints); |
| max | 对数组元素按数值型直接量计算最大值, 返回最大值 | $max = List::Util::max(@ints); |
| sum | 对数组元素按数值型直接量求和, 返回数组元素和 | $sum = List::Util::sum(@ints); |
| minstr | 对数组元素按字符串直接量计算最小值, 返回最小值 | $minstr = List::Util::minstr(@books); |
| maxstr | 对数组元素按字符串直接量计算最大值, 返回最大值 | $maxstr = List::Util::maxstr(@books); |
| shuffle | 对数组随机排序, 返回排序后的数组, 不影响原数组 | @ints_random = List::Util::shuffle(@ints); |


##

# 5. 






