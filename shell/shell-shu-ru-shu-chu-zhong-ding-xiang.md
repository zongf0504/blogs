# Shell 输入输出重定向
> Shell 默认的输入为键盘输入, 默认输出则是终端的Shell 窗口. 但是有时候我们希望某些命令输出的内容不显示,或者想将输出的结果写入其它文件, 这就用到了输出重定向.

## 1. Linux 下的标准输入输出
Linux 下一切皆文件, 输入输出也不例外.虽然标准输入时键盘输入, 标准输出为终端显示器,但是在linux 中标准输入输出也对应着响应的文件. 一般情况下, 每个Linux 命令运行时都会打开三个文件:
* 标准输入文文件(/dev/stdin): 文件描述符为0, linux 程序默认从stdin 读取数据
* 标准正确输出文件(/dev/stdout): 文件描述符为1, linux 程序默认将正确输出写入stdout中
* 标准错误输出文件(/dev/stderr): 文件描述符为2, linux 程序默认会将错误输出写入stderr 文件夹中

```bash
[admin@localhost shell]$ ll /dev/std*
lrwxrwxrwx. 1 root root 15 Jun 19 13:46 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx. 1 root root 15 Jun 19 13:46 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx. 1 root root 15 Jun 19 13:46 /dev/stdout -> /proc/self/fd/1
[admin@localhost shell]$ 
```


