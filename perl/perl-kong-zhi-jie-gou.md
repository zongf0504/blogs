# Perl 控制结构
> 每种编程语言需要有流程控制语法, 无非是判断结构, 循环结构, 这些基本上都大同小异.Perl 也不例外, Perl 也提供了if-else, if-elsif, unless判断结构, while, until, for, foreach 循环结构. 但是,由于perl 有很多简写方式, 所以perl 的控制结构语法写起来更灵活, 用起来更简单.

#1. 控制结构
perl 的判断结构有if 和 unless, 这两种结构都是单匹配结构, 没有类似switch case 这种结构, 需要使用if-elsif-elsif-else 来模拟.

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

```perl
unless ( testExp ){
   codes
}
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










