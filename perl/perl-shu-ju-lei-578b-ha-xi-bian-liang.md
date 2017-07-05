# 哈希

> 哈希是使Perl 语言称为杰出编程语言的关键特色.哈希类似于Java 中的map 类型, 存储的是 key-value 键值对. perl 的哈希是非常高效的, 当hash 的元素有很多时, 查询也是相当快的.

# 1. 哈希特性

哈希结构存储的是一组 key-value 对的集合. 其中key 为字符串, value 为直接量, 可以通过key 来获取value 的值.

哈希和数组比较相似, 只不过数组是通过数字索引来获取元素的值, 哈希是通过字符串key来获取..

* 哈希是无序的
* 哈希的key不能重复, 适合存储一对一, 多对一的关系
* 哈希中可以存储无限多的key-value 对
* 哈希拥有非常高效的算法以保证在哈希元素增多时, 查询速度依然很快
* 哈希可以同时获取多个key的值
* 哈希value还能是单个单数, 即为字符串, 数字, 不能为数组,列表或哈希.

# 2. 哈希基本操作

## 2.1 哈希创建

* 哈希变量命名:  %+变量名, 如: %ip\_hash
* 创建哈希时, 在key的不产生歧义的情况下引号可用省略
* 通常在创建哈希的时候, 会同时为哈希赋值, 有以下几种方式:

```perl
#1. 使用列表创建, 必须为偶数个元素, 奇数为key, 偶数为value
%hash_name= (key1, value1, key2, value2 ...);

#2. 胖箭头创建
%hash_name= (
    key1 => value1, 
    key2 => value2,
    ...,
);

#3. 用哈希为哈希复制
%hash_reverse = reverse %hash_name;

#4. 元素赋值, 如果hash_name不存在, 则会创建一个新的全局哈希变量
$hash_name{key} = value;

#5. 创建一个空的哈希,或清空哈希元素
%hash_name = ();
```

## 2.2 哈希引用

* 哈希元素也是单一的元素, 所以引用的时候也是使用$, 和数组不同的时, 后面跟{}而不是\[\] 

| 哈希赋值 | 语法格式 |
| :--- | :--- |
| 整体引用 | %hash\_name |
| 单个元素引用 | $hash\_name{key} |

## 2.3 哈希元素提取

可以通过@符号提取哈希中的多个元素为数组, 因为提取的为多个元素,所以用的是@而不是$.

* 提取为数组: @array=@hash\_name{key1, key2 ..};
* 提取为列表: \($value1, $value2...\) = @hash\_name\(key1, key2 ...\);

## 2.4 字符串内插

* 哈希本身不支持元素内插, 但是哈希元素支持遍历内插, 比如: "$hash\_name{key}"

# 3. 哈希遍历

## 3.1 while-each 遍历

* 遍历结果是随机的, 因为hash 本事就是无序的

```perl
while(my ($key, $val) = each %ips_hash){
    print "$key --> $val \n";
}
```

## 3.2 keys 数组遍历

* 通过获取哈希的keys 列表进行遍历,可以进行有序遍历, 由于数组有很多遍历方式, 这样会衍生出很多遍历方式

```perl
# 获取哈希的keys 数组进行无序遍历
foreach (keys %ip_hash){
    print "$_ --> $bookMap{$_}\n";
}

# 获取哈希的keys 数组进行有序遍历
foreach (sort keys %ip_hash){
    print "$_ --> $bookMap{$_}\n";
}
```

## 3.3 keys-values 遍历

* 单独获取哈希的keys 和 values 是无序的 ,且不能保证以一对应
* 连续获取哈希的keys 和 values 可以保证相同索引的key 和 value 是一一对应的.

```perl
# 依次获取哈希的keys 和 values
@keys = keys %hash_name;
@values = keys %hash_name;

for my $idx ( 0..$#keys){
    print "$keys[$idx] --> $values{$idx}\n";
}
```

# 4. 常用方法

* 单独使用keys 和 vlaues 返回的数组是无序的, 因此返回的元素并不一定是一一对应的. 当使用完keys 之后马上使用values , 那么此时相同索引的key 和 value是一一对应的
* 使用reverse 的时候需要注意, 需要保证value 也是唯一的, 否则会相同的value作为key时, 会随机覆盖,变成不可控.


| 哈希赋值 | 语法格式 | 示例 |
| :--- | :--- | :--- |
| keys | 获取哈希所有的key,返回由key组成的数组 | @keys = keys %hash\_name; |
| values | 获取哈希所有的value, 返回由value组成的数组 | @values = values %hash\_name |
| exists | 判断key 是否在哈希中存在, 存在返回1,否则返回空 | exists %hash\_name{key} |
| delete | 删除哈希中的元素,返回删除key对应的value | $value = delete $hash\_name{key} |
| reverse | 颠倒哈希的key,value, 变成value-key 组合| %hash_reverse = reverse %hash |

# 5. 内置哈希

* perl 语言内置了哈希%ENV, 此哈希存储了当前系统的环境变量,可通过ENV 获取当前环境变量设置的key-value信息, 如: PATH: $ENV{PATH}; JAVA\_HOME: $ENV{JAVA\_HOME};

# 6. 测试用例

## 6.1 测试脚本

```perl
#!/usr/bin/perl

print "\n####################  2.1 创建哈希  ####################i \n";
# 1.列表创建
%char_hs = ( A, 'a', B, 'b', C, 'c');

## 2.胖箭头创建
%ip_hs = (
  "www.taobao.com" => "60.28.242.249",
  "www.baidu.com" => "61.135.169.125",
);

## 3. 使用哈希创建哈希
%ip_reverse = reverse %ip_hs;

## 4. 创建空的hash
#%null_hs = ();

## 5. 哈希元素创建
$book_hash{"java"} = "Think in java ";

print "\n####################  2.2 哈希引用  ####################\n";
# 1. 整体引用
%char_hs2 = %char_hs;

# 2. 元素引用
$char_A = $char_hs{A};
print "char_A:$char_A \n";

print "\n####################  2.3 哈希元素提取  ####################\n";
# 提取为数组
@char_array = @char_hs{A, C};
print "char_array: @char_array \n";

# 提取为列表, 为多个标量赋值
($char_B, $char_C) = @char_hs{A, C};
print "char_B: $char_B, char_C: $char_C \n";

print "\n####################  2.4 哈希元素字符串内插  ####################\n";
print "char_A:$char_A, char_B: $char_B, char_C: $char_C \n";

print "\n####################  3.1 while-each 遍历  ####################\n";
print "while-each 无序遍历 ip_hash: \n";
while (my ($key, $val) = each %ip_hs) {
    print "$key -> $val \n";
}

print "\n####################  3.2 foreach 遍历  ####################\n";
print "foreach 有序遍历 char_hs: \n";
foreach (sort keys %char_hs){
   print "$_ -> $char_hs{$_} \n";
}

print "\n####################  3.3 keys-values 遍历  ####################\n";
@keys = keys %char_hs;
@values = values %char_hs;

for my $idx (0..$#keys) {
   print "$keys[$idx] -> $values[$idx] \n";
}

print "\n####################  4. 常用方法  ####################\n";
# keys
@keys1 = keys %char_hs;
@keys2 = keys %char_hs;
print "char_hs keys: @keys1 \n";
print "char_hs keys: @keys2 \n";

# exists
$c = exists $char_hs{C};
$d = exists $char_hs{D};
print "exists: c:$c, d:$d \n";

# delete 
$del = delete $char_hs{B};
@keys = keys %char_hs;
print "delete: B -> $del, left: @keys \n";

# reverse
%r_ip_hs = reverse %ip_hs;
print "reverse: r_ip_hs \n";
while (my ($key, $val) = each %r_ip_hs) {
    print "$key -> $val \n";
}

#哈希数量
$length = keys %char_hs;
print "char_hs length: $length \n";

# 清空
%char_hs = ();
$length = keys %char_hs;
print "length after empty: $length \n";


print "\n####################  5. 内置哈希  ####################\n";
$JAVA_HOME = $ENV{JAVA_HOME};
$PATH = $ENV{PATH};
print "ENV: JAVA_HOME=$JAVA_HOME, PATH=$PATH \n";
```

## 6.2 脚本输出

```bash
[admin@localhost perl]$ ./hash.pl 

####################  2.1 创建哈希  ####################i 

####################  2.2 哈希引用  ####################
char_A:a 

####################  2.3 哈希元素提取  ####################
char_array: a c 
char_B: a, char_C: c 

####################  2.4 哈希元素字符串内插  ####################
char_A:a, char_B: a, char_C: c 

####################  3.1 while-each 遍历  ####################
while-each 无序遍历 ip_hash: 
www.taobao.com -> 60.28.242.249 
www.baidu.com -> 61.135.169.125 

####################  3.2 foreach 遍历  ####################
foreach 有序遍历 char_hs: 
A -> a 
B -> b 
C -> c 

####################  3.3 keys-values 遍历  ####################
A -> a 
C -> c 
B -> b 

####################  4. 常用方法  ####################
char_hs keys: A C B 
char_hs keys: A C B 
exists: c:1, d: 
delete: B -> b, left: A C 
reverse: r_ip_hs 
60.28.242.249 -> www.taobao.com 
61.135.169.125 -> www.baidu.com 
char_hs length: 2 
length after empty: 0 

####################  5. 内置哈希  ####################
ENV: JAVA_HOME=/opt/app/jdk/jdk1.6.0_31, PATH=/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/perl/bin:/usr/perl/tools:/opt/app/jdk/jdk1.6.0_31/bin:/opt/app/mongo/mongodb-linux-x86_64-rhel62-3.4.2/bin:/opt/app/maven/apache-maven-2.2.1/bin:/home/admin/bin
```



