# Perl 控制结构
> 每种编程语言需要有流程控制语法, 无非是判断结构, 循环结构, 这些基本上都大同小异.Perl 也不例外, Perl 也提供了if-else, if-elsif, unless判断结构, while, until, for, foreach 循环结构. 但是,由于perl 有很多简写方式, 所以perl 的控制结构语法写起来更灵活, 用起来更简单.

#1. 控制结构
perl 常用的控制结构:

* if-else: 如果为真, 执行if代码块, 否则执行else 代码块
* if-elsif-else: 如果为真, 执行if代码块, 否则如果elsif 为真执行elsif 代码块, 否则执行else 代码块
* unless: 除非

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











