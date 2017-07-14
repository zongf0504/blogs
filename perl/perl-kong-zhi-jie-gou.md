# Perl 控制结构
> 每种编程语言需要有流程控制语法, 无非是判断结构, 循环结构, 这些基本上都大同小异.Perl 也不例外, Perl 也提供了if-else, if-elsif, unless判断结构, while, until, for, foreach 循环结构. 但是,由于perl 有很多简写方式, 所以perl 的控制结构语法写起来更灵活, 用起来更简单.

#1. 控制结构
perl 的判断结构有if 和 unless, 这两种结构都是单匹配结构, 没有类似switch case 这种结构, 需要使用if-elsif-elsif-else 来模拟. 其实unless 等价于 if (! testExp) .

## 1.1 if判断结构

### 1.1.1 if 结构
* 如果测试表达式testExp为真, 则执行代码块code
* 简写形式, 只能执行一句语句

```perl
# 普通形式
if ( testExp ) {
   codes;
}

# 简写形式
code if testExp;

```

### 1.1.2 if-else
* if-else: 如果测试表达式为真, 则执行代码块儿codes1, 否则执行代码块儿codes2
```perl
if( testExp ){
   codes1
} else {
   codes2
}
```

### 1.1.3 if-elsif
* if-elsif: 如果表达式1为真, 则执行代码块儿codes1; 如果表达式2为真, 则执行代码块儿2
* elsif 块儿可以有多个
* 注意关键字为elsif, 而不是elseif

```perl
if ( testExp1 ) {
   codes1
} elsif ( testExp2 )
   codes2
}
```

### 1.1.4 if-elsif-else
* if-elsif-else: 如果表达式1为真, 则执行代码块儿codes1; 如果表达式2为真, 则执行代码块儿2, 否则执行codes3

```perl
if ( testExp1 ) {
   codes1
} elsif ( testExp2 )
   codes2
} else {
   codes3
}
```

## 1.2. unless 结构
* unless 可以翻译为除非, 如果不的意思
* unlesss 和 if 正好相反, if 是测试表达式为真时执行, 而unless 则是判断表达式不为真时执行

### 1.2.1 unless 结构
* 如果testExp 不为真则执行codes
* 简写形式只能执行一条语句, 但是却很常用.
```perl
## 标准写法
unless ( testExp ){
   codes
}
## 简写
code unless testExp;
```

### 1.2.2 unless-else 结构
* 如果测试表达式不为真时执行codes1, 否则执行codes2

```perl
unless ( testExp ){
   codes1
} else {
   codes2
}

```

# 2. 循环结构
perl 的循环结构有, for, foreach, while, until 四种, 控制循环语句有last, next, redo三种方式.其中for 和 foreach 底层结构一样, 它俩是等价的. while 和 until 是相反的逻辑,类似于if 和 unless 的关系.

## 2.1 for 循环
* perl 中的for 循环和其它语言for循环用法相同

```perl
for( 变量初始化; 测试表达式 ; 变量变化) {
   codes;
}
```

## 2.2 foreach 循环
* perl 语言支持其它语言未必不支持的foreach循环, 主要用于遍历数组或列表, 非常好用
* 和for等价, 用for关键字也行

```perl
foreach 变量定义 (数组/列表){
   codes
}
```

## 2.2 while 循环
* 当表达式为真时, 会执行循环体, 直到表达式为假或遇到last 语句后终止循环

```
while (testExp){
    codes;
}
```

## 2.3 until 循环
* 当表达式为假时, 会执行循环体, 直到表达式为真或遇到last 语句后终止循环

```
until (testExp){
    codes;
}
```

# 3. 循环控制
perl 控制循环的语句有: next, last, redo:
* next: 类似于java 中的continue, 跳过本次循环, 进行下一次循环
* last: 类似于java 中的break, 直接结束最近的一层循环结构,执行循环结构后的语句
* redo: 重新执行本次循环








