# Perl 简介

Perl 是Practical Extraction and Report Language 的缩写，可翻译为 "实用报表提取语言"。Perl 是一门语法很灵活, 执行效率很高的语言, 有巨大的第三方代码库CPAN支持, 因此可以做到很多事情. 但是,笔者通常把Perl 当作一种高级的脚本语言, 用来开发一些常用的linux 工具, 编写一些日常服务器维护的脚本.

## perl 的优点
* linux 自带安装环境


# perl 脚本规范
* 文件名以.pl 结尾
* 语句以分号; 结尾
* 可以适当添加空格, 空行以缩进美化代码

# 2. perl 环境
* windows: 系统默认情况下是不安装perl 环境, 需要手工安装ActivePerl软件. 
* Linux: 通常情况下Linux 系统默认集成了perl 环境
笔者通常使用perl 来编写linux 下的脚本以辅助日常工作, 所以示例代码全部在linux 环境编写, 所以对于windows 下安装perl 就不过多介绍了. 下面我们检测一下perl的安装情况

## 2.1 查看perl 版本
笔者系统为Centos 6.8 x64 系统, 默认安装的版本为5.10:

```bash
[admin@localhost perl]$ perl -v

This is perl, v5.10.1 (*) built for x86_64-linux-thread-multi

Copyright 1987-2009, Larry Wall

Perl may be copied only under the terms of either the Artistic License or the
GNU General Public License, which may be found in the Perl 5 source kit.

Complete documentation for Perl, including FAQ lists, should be found on
this system using "man perl" or "perldoc perl".  If you have access to the
Internet, point your browser at http://www.perl.org/, the Perl Home Page.
```

## 2.2 查看perl 安装位置
```bash
[admin@localhost perl]$ which perl
/usr/bin/perl
```

# 3. Perl 入门程序
通常一说到入门程序, 肯定是输出一个Hello,world!, 当然了Perl 语言也不例外.

## 3.1 创建一个helloworld 脚本
使用文本编辑器, 比如说vim , 创建一个hello.pl 文件. 文件内容如下:
* 第一行: 指定perl 程序安装位置
* 第二行: 空白行, 可随意添加, 美化代码格式
* 第三行: 单行注释, 已# 开头. perl 中没有多行注释
* 第四行: 输出语句, 输出一行字符

``` perl
#!/usr/bin/perl

#输出一行
print "Hello,world! Hello,perl!";
```

## 3.2 赋予脚本可执行权限
Linux 下默认创建文件是么有可执行权限的, 而脚本若想运行则必须拥有可执行权限. 通常笔者会为脚本赋予755 权限, 755 权限含义:
* 用户: 可读, 可写, 可执行
* 用户组: 可读, 可执行
* 其它人: 可读, 可执行

```bash
[admin@localhost perl]$ ll
total 4
-rw-r--r--. 1 admin admin 72 Jul  4 13:26 1.hello.pl
[admin@localhost perl]$ chmod a+x 1.hello.pl 
[admin@localhost perl]$ ll
total 4
-rwxr-xr-x. 1 admin admin 72 Jul  4 13:26 1.hello.pl
```

## 3.3 执行脚本
### 3.3.1 使用相对路径执行
```bash
[admin@localhost perl]$ ./hello.pl 
Hello,world! Hello,perl!
```
### 3.3.2 使用绝对路径执行
```bash
[admin@localhost perl]$ /home/admin/blog/perl/hello.pl 
Hello,world! Hello,perl!
```

