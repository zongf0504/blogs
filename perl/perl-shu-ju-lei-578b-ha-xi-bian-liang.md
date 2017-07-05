# 哈希

> 哈希是使Perl 语言称为杰出编程语言的关键特色.哈希类似于Java 中的map 类型, 存储的是 key-value 键值对. perl 的哈希是非常高效的, 当hash 的元素有很多时, 查询也是相当快的.

# 1. 哈希特性

哈希结构存储的是一组 key-value 对的集合. 其中key 为字符串, value 为直接量, 可以通过key 来获取value 的值.和数组比较相似, 只不过数组是通过索引来获取元素的值, 哈希是通过key来获取. 数组的索引只能为数字, 而哈希的索引为字符串.

* 哈希是无序的
* 哈希的key不能重复, 适合存储一对一, 多对一的关系
* 哈希是无序的
* 哈希中可以存储无限多的key-value 对
* 哈希拥有非常高效的算法以保证在哈希元素增多时, 查询速度依然很快

# 2. 哈希基本操作

## 2.1 哈希定义

* 哈希变量命名:  %+变量名, 如: %ip\_hash
* 通常在定义哈希的时候, 会同时为哈希赋值
* 哈希定义时, key的在不产生歧义的情况下可用省略
* 哈希可以不定义直接而直接引用其哈希元素, 这样会默认创建全局哈希变量

```perl
#1. 数组定义, 必须为偶数个元素, 奇数为key, 偶数为value
%hash_name= (key1, value1, key2, value2 ...);

#2. 胖箭头定义
%hash_name= (
    key1 => value1, 
    key2 => value2,
    ...,
);

#3. 元素定义
$hash_name{key} = value;
```

## 2.2 哈希赋值

| 哈希赋值 | 语法格式 |
| :--- | :--- |
| 哈希赋值 | 列表直接量赋值, 胖箭头赋值 |
| 哈希元素赋值 | $hash_name{key} = value; |
| 哈希复制 | %hash_name = %hash; |
| 哈希置空 | %hash_name = (); |

## 2.3 哈希引用
* 哈希元素也是单一的元素, 所以引用的时候也是使用$, 和数组不同的时, 后面跟{}而不是[] 

| 哈希赋值 | 语法格式 |
| :--- | :--- |
| 哈希引用 | %hash_name |
| 哈希元素引用 | $hash_name{key} |

# 3. 哈希遍历
## 3.1 while-each 遍历
* 遍历结果是随机的, 因为hash 本事就是无序的

```perl
while(my ($key, $val) = each %ips_hash){
	print "$key --> $val \n";
}
```


## 3.2 数组遍历
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

# 4. 常用方法

# 5.


