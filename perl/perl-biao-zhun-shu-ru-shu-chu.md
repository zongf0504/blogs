# Perl 标准输入输出
> 默认情况下, Perl 程序标准输入为键盘输入, 标准输出为终端显示器.


## 1. 标准输入
* 使用<STDIN> 可以获取用户从键盘输入的一行信息, Enter 键结束输入, 输入结果包含换行符
* 通常会使用chomp函数去掉行尾的换行符, 使用标量接收键盘输入, 然后再做后续处理

```perl

#常见用法
print "please input :";
chomp($line=<STDIN>);
print "your input is : $line\n";

```


## 2. 标准输出
* perl 程序的标准输出为用户终端显示器,可以使用print, printf, say来输出

### 2.1 print 
* print 输出一行字符串, 末尾不添加换行符
* print 后跟双引号, 可以实现变量内插, 转义字符, 跟单引号则不会进行转换



