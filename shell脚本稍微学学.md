---
title: shell脚本稍微学学
id: 77
date: 2024-12-03 17:09:54
auther: yutanbird
cover: 
excerpt: 稍微学习记录以下shell脚本，怕以后不会写了
permalink: /archives/shellscriptlearning1
categories:
 - linux
tags: 
 - shell
---



# 前言

&emsp;shell本身是一个用C语言编写的程序，用户与Linux的桥梁，桥梁所使用的语言叫做shellscript，也就是本文的shell脚本。

&emsp;shell环境常见的有：

- Bourne Shell（/usr/bin/sh或/bin/sh）
- Bourne Again Shell（/bin/bash）
- C Shell（/usr/bin/csh）
- K Shell（/usr/bin/ksh）
- Shell for Root（/sbin/sh）
- ……

代码语法参考网站：[shellcheck](https://www.shellcheck.net/wiki)

# 第一行

```shell
#!/bin/bash
```

&emsp;许多脚本第一行都有`#!`开头的语句，这是告诉计算机，有后面的路径`/bin/bash`程序来执行该脚本。除此之外，Python等脚本均可以有该行，当然这并不是必须的一行。

注释是以`#`开头的，此外，还有多行注释。

```shell
:<<EOF
注释内容...
注释内容...
EOF

:<<!
注释内容...
注释内容...
!
```



# 变量

变量名的命名规则与C语言一致，就按照正常写代码命名规范肯定没错，可以使用的符号是下划线`_`。

## 变量赋值

- 显式赋值：注意，等号左右不能有空格

```shell
my_name="runoob"  # 显式赋值
my_name="lmzyoyo" # 更改变量
```

- 语句赋值，详见[for](#for)循环

```shell
# 例子1
for loop in 1 2 3 4 5
do
	echo "The value is: $loop"  # 其中的echo见后续
done

# 例子2
for file in `ls /etc`
# 或
for file in $(ls /etc)
```

## 使用变量

使用变量并不像Python，C/C++里面，直接使用的方式，需要使用`$`美元符号。字符串拼接请看[字符串](#字符串)、echo请看[打印](#打印)

```shell
my_name="runoob"
echo $my_name
echo ${my_name}  # 更推荐这种写法，可以把变量给分隔开
echo $my_nameis  # 这样则会打印空的
echo ${my_name}is # 这样就可以打印 runoobis
```

**打印结果**

```shell
runoob
runoob

runoobis
```

## 只读变量

关键字`readonly`可以声明一个变量为只读的

```shell
myUrl="https://lmzyoyo.top"
readonly myUrl
myUrl="https://hexo.lmzyoyo.top"  # 会报错, myname: is read only
```

## 删除变量

关键字`unset`可以删除一个变量

```shell
myUrl="lmzyoyo.top"
unset myUrl
echo ${myUrl}  # 没有任何输出
```

## 字符串

### 引号区别

&emsp;字符串可以由单引号`''`与双引号`“”`两种方式来表示一个字符串，并且字符串拼接可以直接写两个字符串即可。区别在于：

- 单引号中不能有变量，只能原样输出
- 单引号不能使用转义字符

```shell
your_name="runoob"
# 使用双引号拼接
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"
echo $greeting  $greeting_1

# 使用单引号拼接
greeting_2='hello, '$your_name' !'
greeting_3='hello, ${your_name} !'
echo $greeting_2  $greeting_3
```

**打印结果**：

```shell
hello, runoob ! hello, runoob !
hello, runoob ! hello, ${your_name} !
```

### 字符串操作

- 获取字符串长度

```shell
myUrl="lmzyoyo.top"
echo ${#myUrl}  # 输出11
echo ${#myUrl[0]} # 输出11

${#myUrl} 等同于 ${#myUrl[0]}
```

- 按照下标截取字符串：字符串正向下标从0开始，逆向下标从1开始

```shell
myUrl="lmzyoyo.top"
echo ${myUrl:3:2}  # 输出yo
echo ${myUrl:3} # 输出yoyo.top
echo ${myUrl:0-2:2} # 输出op
echo ${myUrl:0-5} # 输出o.top

# echo ${变量名:起始下标:长度} 
# echo ${变量名:起始下标}
# echo ${变量名:0-起始下标:长度}  
# echo ${变量名:0-起始下标}
```

- 按照字符截取字符串：下面的`str`还可以可以携带`*`通配符，且截取到的字符串均不包括`str`，如果`str`匹配失败，则返回整个字符串
    - `${name#str}`，从左到右，截取最短满足 `str` 字符串的右边子字符串
    - `${name##*str}`，从左到右，截取最长满足 `str` 字符串的右边子字符串
    - `${name%str*}`，从右到左，截取最短满足 `str` 字符串的左边子字符串
    - `${name%%str*}`，从右到左，截取最长满足 `str` 字符串的左边子字符串

```shell
myUrl="11223344551122abc"
echo ${myUrl#*33*55}  # 说明 * 可以多次使用
echo ${myUrl#*22}   # 从左往右，截取满足 "*22" 最短的字符串 的右边
echo ${myUrl##*22}  # 从左往右，截取满足 "*22" 最长的字符串 的右边
echo ${myUrl%22*}   # 从右往左，截取满足 "22*" 最短的字符串 的左边
echo ${myUrl%%22*}  # 从右往左，截取满足 "22*" 最长的字符串 的左边
```

**结果**

```shell
1122abc
3344551122abc
abc
112233445511
11
```

- 查找字符串：没有专门查找字符串的函数或者命令，需要依靠其他命令，如`grep`等

## 数组

### 定义

shell仅仅支持一维数组，数组下标可以为结果大于等于0的表达式

```shell
my_info=("lmzyoyo" 1 34 3.2 "top")
# 或者
my_info=(
	"lmzyoyo" 
	1 
	34 
	3.2 
	"top"
)
# 或者
my_info[0]="lmzyoyo"
my_info[1]=1
my_info[2]=34
# my_info[3]=3.2  # 如果未设置中间某个元素，那么默认为空
my_info[4]="top"
# 或者
my_info=([1]=1 [2]=34)
```

### 获取元素

`${name[index/@/*]}`，其中`name`表示数组名字，下标可以是非负整数，也可以是`@`或者`*`

```shell
my_info[0]="lmzyoyo"
my_info[1]=1
my_info[2]=34
# my_info[3]=3.2
my_info[4]="top"
echo "${my_info[1]}"
echo "${my_info[@]}"
echo "${my_info[*]}"
```

**结果**

```shell
1
lmzyoyo 1 34 top
lmzyoyo 1 34 top
```

### 获取长度

```shell
echo ${#my_info[@]} # 获取数组的长度
echo ${#my_info[*]} # 获取数组的长度
echo ${#myinfo[0]} # 获取第0个元素的长度
```

## 关联数组

关联数组相当于map，使用关键字`declare`声明，可以先声明再赋值，也可以在声明的同时赋值

```shell
declare -A site=(["Google"]="https://google.com" ["baidu"]="https://baidu.com")
# 或者
declare -A site
site["Google"]="https://google.com"
site["baidu"]="https://baidu.com"

echo "${site["baidu"]}"  # 打印 https://baidu.com
# 同理，其下标仍然可以为@或者*，来获取所有的值

# 获取所有的键
echo "${!site[*]}"

# 不存在获取单个元素的键
echo "${!site["baidu"]}" # 错误的
```

# 外界参数传递

我们调用一个shell脚本，常常是 `/path/demo.sh -x arg -y arg ...`，这其中就有参数

| 参数 |                           说明                            |
| :--: | :-------------------------------------------------------: |
| `$n` |           其中n是数字，从0开始，0表示该脚本名字           |
| `$#` |                  传递给脚本的参数的个数                   |
| `$*` |  以一个字符串显示所有传递给脚本的参数，等同于“\$1 \$2 …”  |
| `$!` |                 后台运行的最后一个进程ID                  |
| `$@` | 以一个字符串显示所有传递给脚本的参数，等同于“\$1” “\$2” … |
| `$-` |        显示Shell使用的当前选项，与set命令共能相同         |
| `$?` |   显示最后命令的退出状态，0表示没有错误，其他值表示出错   |
| `$$` |                   脚本运行的当前进程ID                    |

# 运算符

运算符包括

- [算数运算符](#算数运算符)
- [关系运算符](#关系运算符)
- [布尔运算符](#布尔运算符)
- [逻辑运算符](#逻辑运算符)
- [字符串运算符](#字符串运算符)
- [文件测试运算符](#文件测试运算符)

## 算数运算符

这里不得不介绍一个命令`expr`，`expr`是用来计算一个表达式的，后面跟着一个表达式。

- 值得注意的地方是：
    - 1、符号\`括起来的表示一条命令，并且把返回结果当成表达式的值，除此之外还可以写成\$(命令)，并且现在推荐写\$(命令)
    - 2、`expr $a + $b`中间的空格均不可省略。现在更加推荐写法是：`$((a + b))`，中间空格可以省略，并且不需要写成`$a $b`，直接写`a b`即可。
    - 3、注意下面`expr $a \* $b`中，由于`*`是特殊符号，常常代表通配符，因此这里需要转义。
    - 4、if语句请看后面的[if](#if)小节，这里稍微提一下后面条件的写法。`[ $a == $b ]`所有的空格均不能省略。

```shell
a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi
if [ $a != $b ]
then
   echo "a 不等于 b"
fi
```

**结果**

```shell
a + b : 30
a - b : -10
a * b : 200
b / a : 2
b % a : 0
a 不等于 b
```

## 关系运算符

到这里，我们可能发现，只有不等于，没有大于、小于等符号，这些在shell中称为关系运算符。

下面`a=10、b=20`

| 运算符 |                             说明                             |             举例             |
| :----: | :----------------------------------------------------------: | :--------------------------: |
|  -eq   |          $==$，检测两个数是否相等，相等返回 true。           | [ \$a -eq \$b ] 返回 false。 |
|  -ne   |        $!=$，检测两个数是否不相等，不相等返回 true。         | [ \$a -ne \$b ] 返回 true。  |
|  -gt   |    $>$，检测左边的数是否大于右边的，如果是，则返回 true。    | [ \$a -gt \$b ] 返回 false。 |
|  -lt   |    $<$，检测左边的数是否小于右边的，如果是，则返回 true。    | [ \$a -lt \$b ] 返回 true。  |
|  -ge   | $\geq$，检测左边的数是否大于等于右边的，如果是，则返回 true。 | [ \$a -ge \$b ] 返回 false。 |
|  -le   | $\leq$，检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ \$a -le \$b ] 返回 true。  |

## 布尔运算符

布尔运算，相当于与或非运算，也就是常见语言中的逻辑运算符。这里是单括号

下面`a=10、b=20`

| 运算符 |                        说明                         |                    举例                    |
| :----: | :-------------------------------------------------: | :----------------------------------------: |
|   !    | 非运算，表达式为 true 则返回 false，否则返回 true。 |          [ ! false ] 返回 true。           |
|   -o   |      或运算，有一个表达式为 true 则返回 true。      |  [ \$a -lt 20 -o $b -gt 100 ] 返回 true。  |
|   -a   |      与运算，两个表达式都为 true 才返回 true。      | [ \$a -lt 20 -a \$b -gt 100 ] 返回 false。 |

## 逻辑运算符

shell中也有逻辑运算符，相当于上述的与或。但是这里是双括号

下面`a=10、b=20`

| 运算符 | 说明       | 举例                                         |
| :----- | :--------- | :------------------------------------------- |
| &&     | 逻辑的 AND | [[ \$a -lt 100 && \$b -gt 100 ]] 返回 false  |
| \|\|   | 逻辑的 OR  | [[ \$a -lt 100 \|\| \$b -gt 100 ]] 返回 true |

## 字符串运算符

字符串有其专门的运算符，假设`a="abc"、b="efg"`

| 运算符 |                     说明                     |            举例            |
| :----: | :------------------------------------------: | :------------------------: |
|   =    |   检测两个字符串是否相等，相等返回 true。    | [ \$a = ​\$b ] 返回 false。 |
|   !=   | 检测两个字符串是否不相等，不相等返回 true。  | [ \$a != \$b ] 返回 true。 |
|   -z   |    检测字符串长度是否为0，为0返回 true。     |   [ -z $a ] 返回 false。   |
|   -n   | 检测字符串长度是否不为 0，不为 0 返回 true。 |  [ -n "$a" ] 返回 true。   |
|   $    |   检测字符串是否不为空，不为空返回 true。    |     [ $a ] 返回 true。     |

**注意：** `-z`可以不加双引号，`-n`必须要加双引号。

## 文件测试运算符

| 操作符  | 说明                                                         | 举例                      |
| :------ | :----------------------------------------------------------- | :------------------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |
| -S file | 判断文件是否socket                                           | [ -S $file ]              |
| -L file | 判断文件是否存在并且是一个符号链接                           | [ -L $file ]              |

# 流程控制

shell的流程控制不可以为空，也就是if等的代码体中不可以为空。

## if

任何`if`语句需要有`fi`来作为结尾，相当于其他语言中的大括号一样。

- 单行`if`语句，需要用分号`;`来分隔。适用于命令行中

```shell
if condition ; then command1; command2; ... commandN; fi
```

- 多行`if`语句，then需要另起一行

```shell
if condition
then
	command1
	command2
	command3
	...
fi
```

- 带`else`语句

```shell
if condition
then
	command1
	command2
	...
else
	command3
	command4
	...
fi
```

- 带`else-if`语句

```shell
if condition1
then
	commands
elif condition2
then
	commands
...
else
	commands
fi
```

- 下面为一个例子：

```shell
#!/bin/bash
a=89

if [ $a -lt 60 ]
then
	echo "成绩为：$a"
	echo "成绩不及格，是真的不行"
elif [ $a -lt 70 ]
then
	echo "成绩为：$a"
	echo "成绩为及格，需要加油"
elif [ $a -lt 100 ]
then 
	echo "成绩为：$a"
	echo "成绩为优秀，继续努力"
else
	echo "成绩为：$a"
	echo "成绩不正常"
fi

```

## for

`for`循环中由`do-done`来将代码块包裹起来

- 写成一行，表示`var`为名字的变量依次取`item1 - itemN`

```shell
for var in item1 item2 ... itemN; do command1; command2; ... commandN; done
```

- 写成多行：注意很多地方都需要另起一行

```shell
for var in item1 item2 ... itemN;
do
	command1
	command2
	...
	commandN
done
```

均表示，变量名为`var`的变量依次取`item1` — `itemN`。其中`item1 item2 ... itemN`可以由其他命令的结果，则使用反引号\`来括着的命令或者是$(命令)

## while语句

- 写成一行

```shell
while condition; do command1; command2; ... commandN; done
```

- 写成多行：注意另起一行的地方需要另起一行

```shell
while condition
do
	command1
	command2
	...
	commandN
done
```

condition条件为真的时候，执行`do-done`中间的代码块

## until循环

- 写成一行

```shell
until condition; do command1; command2; ... commandN; done
```

- 写成多行

```shell
until condition
do 
	command1
	command2
	...
	commandN
done
```

condition条件为假的时候，执行`do-done`中间的代码块

## case分支

```shell
case 值 in
模式1)
	command1
	...
	;;
模式2|模式3|模式4|模式5)
	command2
	...
	;;
...
*)
	commandN
	...
	;;
esac
```

注意，可以使用 `|` 来涵盖多个模式，也可以使用 `*` 来通配所有模式。这里不像C/C++一样，会依次执行符合分支之后的分支，这里只会执行一个分支，不需要`break`命令。

## break与continue

在循环中，仍然可以使用`break`与`continue`两个关键字来跳出循环，与常规的编程语言一样。

# 函数

## 函数定义

函数必须先定义，再调用，这个和一般的语言一样

函数定义如下：

```shell
[ function ] functionName [()]
{
	code block;
	[return int;]
}
```

- 其中，`function`关键字可有可无
- 其中，`return`关键字可有可无，如果没有，则会返回最后一条命令运行结果。

```shell
sayHello()
{
	echo "hello world"
}
```

如果函数需要参数呢？那也不会写在括号里面。参数传递相当于[外界参数传递](#外界参数传递)一样。

```shell
sayNHello()
{
	i=0
	while [ $i -lt $1 ]
	do
		echo "hello"
		i=$((i+1))
	done
	
	for v in {1..10..2}  # 可以使用 $(seq 1 2 10) 替代
	do
		echo "$2 ${v}"
	done
}
# 这个例子中演示了循环n次的几种写法
```



## 函数调用

调用函数，如果没有参数，只需要写函数名即可调用，不能加括号

函数调用完，可以使用`$?`来获取函数调用的值，注意，`$?`是获取上一条命令的返回值，也就是说，在调用函数与执行`$?`命令中间，不能有多余的命令

```shell
echo "开始调用函数"
sayHello
# sayHello()  # 报错
echo "停止调用函数"
```

带参数的调用如下

```shell
sayNHello 3 "world"
# echo "hello" # 如果这条命令不注释，那么下面 $? 获取的是这条命令的返回值
echo "$?" # 获取函数的返回值
```

# 重定向

|            命令            |                            说明                             |
| :------------------------: | :---------------------------------------------------------: |
|       command > file       |                    将输出重定向到 file。                    |
|       command < file       |                    将输入重定向到 file。                    |
|      command >> file       |              将输出以追加的方式重定向到 file。              |
|          n > file          |           将文件描述符为 n 的文件重定向到 file。            |
|         n >> file          |     将文件描述符为 n 的文件以追加的方式重定向到 file。      |
|           n >& m           |                  将输出文件 m 和 n 合并。                   |
|           n <& m           |                  将输入文件 m 和 n 合并。                   |
|           << tag           |     将开始标记 tag 和结束标记 tag 之间的内容作为输入。      |
| command < infile > outfile | 同时输入输出重定向，从infile输入，执行命令后，输出到outfile |

- 文件描述符：
    - 0：表示标准输入（STDIN）
    - 1：表示标准输出（STDOUT）
    - 2：表示标准错误输出（STDERR）

屏蔽所有的输出和错误输出

```shell
command > /dev/null 2>&1
```

还有一种文档重定向，形式如下

```shell
command << delimiter
	document
delimiter
```

其中，`delimiter`表示为一个文档分隔符，后一个必须顶格写。有没有感觉这种形式跟注释很像？下面写一个例子

```shell
wc -l << EOF
	这个是计算
	这个document字段
	的行数
	结果应该输出4
EOF

:<<EOF
	这里写的是多行注释
EOF
```

# 其他

## 文件包含

&emsp;可以使用`. filepath`或者是`source filepath`来包含其他文件，相当于`#include`，也就是相当于执行一遍其他文件。

## echo

- 显示普通字符，显示结果为 `hello`

```shell
echo "hello" # 推荐这种写法
echo 'hello'
echo hello # 这种写法也没错
```

- 显示转义字符，显示结果为 `"hello"`

```shELL
echo "\"hello\""  # 外层双引号可以省略，但不推荐
```

- 显示变量

```shell
read name  # read命令表示从标准输入读取到name里面
echo "$name is your input"
```

- 开启转义

```shell
echo -e "It is a test \n" # 不加 -e ，会原样输出 It is a test \n
```

- 显示不换行

```shell
echo -e "It is a \c" # \c表示显示完不换行，注意是小写字母 c
echo "test"
```

- 显示结果重定向，参考[重定向](#重定向)

```shell
echo "hello" > myfile
```

- 显示命令结果

```shell
echo `date`
echo $(date)
echo $((1+3))
```

- 原样输出，单引号中间的会原样输出

```shell
echo 'hello\"dfasdf"dsfa\n'
```

## printf

相比于`echo`，`printf`更加适合移植，其由[POSIX](https://zh.wikipedia.org/zh-cn/%E5%8F%AF%E7%A7%BB%E6%A4%8D%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E6%8E%A5%E5%8F%A3)标准所定义。格式控制符与C语言类似。

```shell
printf format-string [argu1 argu2 ... arguN]
```

同样，`printf`也支持转义字符，与C语言一样。

## test

`test`用于测试条件是否成立，由以下参数。

|   参数    |                 说明                 |
| :-------: | :----------------------------------: |
| -e 文件名 |          如果文件存在则为真          |
| -r 文件名 |       如果文件存在且可读则为真       |
| -w 文件名 |       如果文件存在且可写则为真       |
| -x 文件名 |      如果文件存在且可执行则为真      |
| -s 文件名 |  如果文件存在且至少有一个字符则为真  |
| -d 文件名 |      如果文件存在且为目录则为真      |
| -f 文件名 |    如果文件存在且为普通文件则为真    |
| -c 文件名 | 如果文件存在且为字符型特殊文件则为真 |
| -b 文件名 |   如果文件存在且为块特殊文件则为真   |

除此之外，test还支持裸机运算

```shell
test -e ./file1.txt -o -e ./file2.txt
```

上述代码中，只要 `./file1.txt`或者`./file2.txt`存在一个即为真。