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
* 通常在创建哈希的时候, 会同时为哈希赋值, 有以下几种种方式:

```perl
#1. 数组定义, 必须为偶数个元素, 奇数为key, 偶数为value
%hash_name= (key1, value1, key2, value2 ...);

#2. 胖箭头定义
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

| 哈希赋值 | 语法格式 | 示例 |
| :--- | :--- | :--- |
| keys | 获取哈希所有的key,返回由key组成的数组 | @keys = keys %hash\_name; |
| values | 获取哈希所有的value, 返回由value组成的数组 | @values = values %hash\_name |
| exists | 判断key 是否在哈希中存在, 存在返回1,否则返回空 | exists %hash\_name{key} |
| delete | 删除哈希中的元素,返回删除key对应的value | $value = delete $hash\_name{key} |

# 5. 内置哈希
* perl 语言内置了哈希%ENV, 此哈希存储了当前系统的环境变量,可通过ENV 获取当前环境变量设置的key-value信息, 如: PATH: $ENV{PATH}; JAVA_HOME: $ENV{JAVA_HOME};




