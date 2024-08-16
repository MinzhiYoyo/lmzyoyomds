---
title: GDB学习简单记录
date: 2024-03-22 21:13:20.0
updated: 2024-03-22 23:52:49.661
url: /archives/gdb学习简单记录
categories: 
- 编程学习
tags: 
- gdb
---



# 什么是GDB

GNU调试器(GNU Debugger，缩写成GDB)，是一个GNU软件系统的调试器，用于调试C/C++程序，且支持终端操作，可以远程或无桌面调试。



# GDB使用前置条件

## 安装GDB

```shell
# Linux
apt-get install gdb
# 或者
yum install gdb

gdb -v # 查看gdb版本信息
```

## 编译待调试程序

> [!TIP] 
>
> 如果是简单用 `g++/gcc` 编译，而不借助cmake，那应该加入`-g`参数

```shell
g++ -g hello.cpp -o hello
```

> [!TIP]
>
> 如果用`cmake`编译程序，那应该在`CMakeLists.txt`加入下面代码

```cmake
SET(CMAKE_BUILD_TYPE "Debug")

# 为debug模式新加参数
SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")

# 为release模式新加参数，与本文无关
SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")

# 具体解释一下上述参数
# -O0与-O3 表示几级优化，debug模式一般不进行编译优化，release版本一般需要3级优化
# -Wall 忽略一些警告信息
# -g -ggdb 为gdb生成调试版本
```

## 开始调试

```shell
# 加入目前有待调试的可执行文件 main
gdb ./main
# 或者
gdb file <文件名> # 比如 gdb file main

# 之后就进入 GDB 模式，会在每行输入前面加了 (gdb) 标识，在后面就可以输入 gdb 命令了

(gdb) 
```

# GDB常用命令

## 简单说明

GDB命令都可以缩写成第一个字母，比如`print`命令，只需要打一个`p`即可。GDB命令回车就可以自动执行上一条命令。

```shell
# 一个非常有用的命令，查看帮助命令
help <命令名称>
# 比如
help display
```



## 设置断点

> [!TIP]
>
> 注意：`b`和`break`完全等同，前者只是后者的缩写，无论其作为参数还是作为命令

```shell
break n # 在当前文件第n行打上断点，可以缩写成 b n
b n     # 上述的缩写，下面的b是break的意思，就都缩写了，b和break同样用法

b n if a > b # 条件断点，当 a > b的时候，在第n行程序暂停

b <function name> # 在函数名为 <function name> 的地方打上断点
b file_name.cpp:n # 在文件file_name.cpp第n行设置断点

info breakpoints    # 查看所有断点信息，可以写成 i b
# 上述命令之后，可以看到许多断点，并且每个断点都有一个唯一的编号
info <断点编号 n>    # 查看编号为n的断点
delete <断点编号 n>  # 删除编号为n的断点，缩写为 d n
disable <断点编号 n> # 暂停编号为n的断点，缩写为 dis n
enable <断点标号 n>  # 使能编号为n的断点，没有缩写
clear <行号 n>      # 清除行号为n的断点，没有缩写
delete breakpoints # 删除所有断点，缩写为 d b
```

> [!NOTE]
>
> 上述命令作为断点来说，基本够用，下面是一些进阶命令

```shell
tbreak  # 设置临时断点，跟break用法一样，区别在于，该断点处程序被执行依次就自动删除断点

watch [-l|-location] <expr> # 为表达式设置断点，当表达式/变量的值改变，那就程序运行暂停，还可以指定参数 -l/-location 对后续表达式<expr>求地址，并检测这个地址的内容是否改变

clear # 不带参数，会清除所选栈在执行源码行中的所有断点
clear <function>    # 清除函数名为 function 的入口断点
clear <filename>:<function>  # 清除文件 filename 文件处的函数 function 的入口断点
clear n  # 清除行号的断点
clear <filename>:n  # 清除文件 filename 文件处第 n 行的断点
```

## 查看代码

> [!TIP]
>
> 查看的命令为`list`，可以缩写成`l`，默认显示前后十行代码，目标行在中间

```shell
list  # 列出当前所在行的代码，前后十行，所在行在中间。或者显示上一次list的下边内容
list n  # 列出当前文件的第 n 行的前后十行，第 n 行在中间
list <function>  # 列出函数名的代码
```

## 运行与执行

> [!TIP]
>
> 运行会直到下个断点才停止，执行就是执行一步

```shell
continue  # 继续运行，直到下个断点。缩写为 c
run  # 重新运行该程序，即从头开始调试该程序。缩写为 r

next  # 执行到下一行，如果该行有函数，则此函数会直接一步执行完。缩写为 n
next [N]  # 单步执行 N 次，直到执行完 N 次或者遇到其他断点
ni    # 执行到下一步，同next一样，不过 next 是用在C/C++等代码上的，ni 是用在汇编上的一次执行

step  # 单步执行，如果该行有函数，则会进入此函数，对此函数内的代码也单步执行。缩写为 s
step [N]  # 单步执行 N 次，直到执行完 N 次或者遇到其他断点
si   # 单步执行，同 step 一样，不过 step 是用在C/C++代码上的，si 是用在汇编上的一次执行

quit  # 退出调试。缩写为 q
```

> [!NOTE]
>
> 上面这三个命令完全够用了，下面为一些进阶

```shell
finish  # 执行直到选择的栈返回，缩写为 fin，当前函数会全部执行完，并返回上一级函数就停止
return # 使选定栈返回其调用者，当前函数后续部分将不会执行，并且可以随意带参数，并返回上一级函数就停止
# 注意区分 finish 和 return 的区别即可

until    # 执行直到答道当前栈中的当前行后的某一行，用来跳过循环或者递归函数调用，缩写为 u
until n  # 运行到行号 n 的位置

frame     # 显示当前堆栈
up [n]    # 改变堆栈显示的深度，改变层数为 n，n省略默认为1
down [n]  # 改变堆栈显示的深度，改变层数为 n，n省略默认为1
```

## 打印变量与信息

>   [!TIP]
>
>   这一小下节讲的是打印程序中的变量与表达式的值，而不是打印调试器的信息

```shell
print <expr>              # 打印表达式/变量的值，任何合法的C/C++等代码的表达式均可，只会打印一次，下一次执行完gdb命令后不会自动追踪表达式的值。缩写成 p
display <expr>            # 打印表达式/变量的值，但是可以在每次执行完gdb命令后都自动打印表达式的值，相当于会自动追踪表达式的值。无缩写形式
undisplay n               # 不显示第n条表达式的监测
info display              # 与 i b 类似，打印所有display的信息
delete display n          # 删除第n条表达式的监测

ptype <expr>              # 打印表达式的类型

set <variable> = <value>  # 改变程序中的某个值
signal <signal name>      # 向程序发送<signal name>的信号，具体参考 Linux 的信号，比如 SIGHINT, SIGHUP
```

>   [!NOTE]
>
>   下面来介绍一些进阶的命令

```shell
backtrace:       # 打印目前的调用栈，遇到 Segment fault （段错误）时候非常好用。缩写为 bt
where:			 # 基本同上，细节每太弄清楚
info stack       # 查看堆栈使用情况
```



## 窗口

>   [!TIP]
>
>   调整显示窗口

```shell
layout      # 用于分割窗口
layout src  # 显示源代码窗口
layout asm  # 显示汇编窗口
layout regs # 显示源代码/汇编和寄存器的窗口
layout next # 显示下一个窗口
layout prev # 显示上一个窗口
CTRL + L  # 刷新窗口
```

# CGDB

再推荐一个比较好用的调试工具，相当于GDB的前端——[cgdb](https://cgdb.github.io/)，[github地址](https://github.com/cgdb)