# Perl 自定义函数(子程序)
> perl 语言支持自定义函数, 通过自定义函数, 我们可以重复利用已有代码, 提供工作效率, 简短代码行数.我们可以封装一些常用的方法存储起来, 以后需要用到的时候,直接拿来用就行了, 不用再重复开发了.


# 1. perl 函数特点
* 函数定义使用关键字 sub + 函数名
* 函数调用使用关键字 & + 函数名
* 函数参数为一个无限制长度的列表直接量
* 函数都有一个返回值, 为一个无限制的列表直接量, 可使用return 返回
* 函数名不能以数字开头, 可由字母,数字,下划线组成
* 函数体由大括号{} 限制
* 函数可在脚本中任意位置定义, 在使用前, 不需要对函数做实现声明
* 函数定义属于全局的

# 2. 变量作用域
perl 语言变量按作用域分,可分为全局变量和局部变量.
* 全局变量: 作用域为整个perl 脚本, 不加关键字my 和 自动创建的变量都属于全局变量
* 局部变量: 定义变量时, 使用my 修饰的变量为全局变量, 作用域为最近的一个{} 之间, 超出这个范围则不能访问定义的变量, 因此局部变量又称私有变量.

## 2.1 循环中定义私有变量

```perl
#定义字符哈希
%char_hs = ( A, 'a', B, 'b', C, 'c');

#获取哈希的key 列表
@keys = keys %char_hs;

#for 中定义的key 和 value 只能在for 循环中访问
for my $key (@keys){
   my $value = $char_hs{$key};
}

print "$key -> $value \n";
```

## 2.2 函数中定义
```perl

# 函数中定义的局部变量只能在函数块儿中访问
sub sayHello(){
   # 如果同时定义两个局部变量, 必须使用()括起来,否则只会定义第一个.
   my (name1, name2) = ("mirror", "zong");
   print "hello, $name1 and $name2\n";
}

&hello;
print "name1:$name1, name2:$name2\n";

```

# 3. 函数定义和调用
* 函数定义: sub funName () {}
* 函数调用: &funName (param...);

```perl
#函数定义
sub sayHello(){
    print "hello,world";
}
#函数调用
&sayHello;
```

# 4. 函数传参
* 调用函数时, 所传入的所有参数都会存入@_数组中, 无论你传的是标量, 数组, 还是哈希, 都会自动转换并存储在@_这个数组中, 可以通过$_[idx]来获取具体参数

## 标量
* 函数只接受一个参数时,通常是用标量接受
* 如果传入为标量, 在函数内修改参数的值, 不会影响标量的值

```perl
sub hello(){
   # 获取参数个数
   my length = @_;
   # 复制参数
   my $param = $_[0];
   # 获取并删除删除
   my $name = shift @_;
}

$name = "mirror";
&hello($name);
```

## 数组
* 函数调用时, 可以传入多个标量或直接量参数, 或一个数组参数, 函数中可用一个数组接收
* 如果函数传入的是一个数组, 在函数内部修改数组元素, 不会影响原来的数组

```perl
sub hello(){
   # 复制参数列表
   my @array = @_;
}

#传入多个标量或直接量参数
& hello("mirror", "ghost");

#传入数组
@names = ("mirror", "ghost");
& hello(@names);
```

## 哈希
* 函数调用时可以传入一个哈希作为参数,
* 函数中修改哈希不会影响原来的哈希

```perl
sub hello(){
  # 复制参数数组为哈希
  my %hash = @_;
  my @keys = keys %hash;
  my @values = values %hash;
  print "keys: @keys, values: @values \n";
}

%char_hs = (A, 'a', B, 'b', C, 'c');
&hello(%char_hs);
```

## 标量数组混合
函数参数可以是标量, 哈希, 数组直接的混合传入, 但是如果哈希和数组进行了混合, 那么在方法内不好分离. 因为函数内部分离的方式是, 先使用shift @_ 将标量弹出, 最后将剩余的参数全部转换为数组或者哈希.
* 标量和数组混合: 只能和一个数组混合, 数组最后传入
* 数组和哈希混合: 只能喝一个哈希混合, 哈希最后传入

```perl
sub hello(){
   #弹出标量
   my $name = shift @_;
   #将剩余参数转换为数组
   my @books = @_;
}

sub hello2(){
   #弹出标量
   my $name = shift @_;
   #将剩余参数转换为哈希
   my %book_hs = @_;
}

@books = ("java", "mysql", "linux");
@char_hs = (A, 'a', B, 'b', C, 'c');

#函数调用
&hello("mirror", @books);
@hello("mirror", $char_hs);

``
# 5. 函数返回值
* perl 中每一个函数都有一个返回值, 返回值底层都是列表, 可使用标量, 数组, 哈希接收.

## 5.1 标量
```perl
sub hello(){
  return "mirrror";
}
$name = &hello();
```

## 5.2 列表
```perl
sub hello(){
   my @books = ("java", "mysql", "linux");
   return @books;
}
@books = &hello();
```

## 5.3 哈希
```perl
sub hello(){
   %char_hs = (A, 'a', B, 'b', C, 'c');
   return %char_hs;
}
%char_hs = &hello();
```

# 6. 测试用例
## 6.1 测试脚本
```perl
#!/usr/bin/perl

#开启警告提示
use strict;

print "\n#################### 函数参数  ####################\n";
#标量
sub param_1(){
  # 从@_中弹出第一个参数,@_中会删除这个参数
  my $param = shift @_;
  print "param: $param\n";
}

#数组
sub param_2(){
  # 复制参数列表@_为@params,对@params 修改不会影响@_数组
  my @params = @_;
  print "params: @params\n";
}

#哈希
sub param_3(){
  # 复制参数数组为哈希数组
  my %hash = @_;
  my @keys = keys %hash;
  my @values = values %hash;
  print "keys: @keys, values: @values \n";
}

#返回-标量
sub return_1(){
  return "mirror";
}

#返回数组
sub return_2(){
  my @arrays = ("mirror", "ghost");
  return @arrays;
}

#返回哈希
sub return_3(){
  %hash = (A, 'a', B, 'b', C, 'c');
  return %hash;
}


############################ 主程序  ####################################

print "\n#################### 测试函数参数:  ####################\n";
$name = "mirror";
@names = ("mirror", "ghost", "zong");
%char_hs = (A, 'a', B, 'b', C, 'c');

#传参:标量
&param_1($name);
#传参:数组
&param_2(@names);
#传参:哈希
&param_3(%char_hs);

print "\n#################### 测试函数返回  ####################\n";
#返回标量
$return_name = &return_1();
print "return_name:$return_name \n";

#返回数组
@return_names = &return_2();
print "return_names:@return_names\n";

#返回哈希
%return_hs = &return_3();
while (my ($key, $val) = each %return_hs){
  print "$key -> $val \n";
}

```

## 6.2 脚本输出
```bash

#################### 函数参数  ####################

#################### 测试函数参数:  ####################
param: mirror
params: mirror ghost zong
keys: A C B, values: a c b 

#################### 测试函数返回  ####################
return_name:mirror 
return_names:mirror ghost
A -> a 
C -> c 
B -> b 
```



