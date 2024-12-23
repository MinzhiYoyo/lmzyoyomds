---
title: bat批处理教程
id: 70
date: 2024-12-03 17:09:53
auther: yutanbird
cover: 
excerpt: 记录一下bat脚本常用教程
permalink: /archives/batscript
categories:
 - default
tags: 
---

# 常用命令
``` bash
C:> help
有关某个命令的详细信息，请键入 HELP 命令名
ASSOC          显示或修改文件扩展名关联。
ATTRIB         显示或更改文件属性。
BREAK          设置或清除扩展式 CTRL+C 检查。
BCDEDIT        设置启动数据库中的属性以控制启动加载。
CACLS          显示或修改文件的访问控制列表(ACL)。
CALL           从另一个批处理程序调用这一个。
CD             显示当前目录的名称或将其更改。
CHCP           显示或设置活动代码页数。
CHDIR          显示当前目录的名称或将其更改。
CHKDSK         检查磁盘并显示状态报告。
CHKNTFS        显示或修改启动时间磁盘检查。
CLS            清除屏幕。
CMD            打开另一个 Windows 命令解释程序窗口。
COLOR          设置默认控制台前景和背景颜色。
COMP           比较两个或两套文件的内容。
COMPACT        显示或更改 NTFS 分区上文件的压缩。
CONVERT        将 FAT 卷转换成 NTFS。你不能转换
               当前驱动器。
COPY           将至少一个文件复制到另一个位置。
DATE           显示或设置日期。
DEL            删除至少一个文件。
DIR            显示一个目录中的文件和子目录。
DISKPART       显示或配置磁盘分区属性。
DOSKEY         编辑命令行、撤回 Windows 命令并
               创建宏。
DRIVERQUERY    显示当前设备驱动程序状态和属性。
ECHO           显示消息，或将命令回显打开或关闭。
ENDLOCAL       结束批文件中环境更改的本地化。
ERASE          删除一个或多个文件。
EXIT           退出 CMD.EXE 程序(命令解释程序)。
FC             比较两个文件或两个文件集并显示
               它们之间的不同。
FIND           在一个或多个文件中搜索一个文本字符串。
FINDSTR        在多个文件中搜索字符串。
FOR            为一组文件中的每个文件运行一个指定的命令。
FORMAT         格式化磁盘，以便用于 Windows。
FSUTIL         显示或配置文件系统属性。
FTYPE          显示或修改在文件扩展名关联中使用的文件
               类型。
GOTO           将 Windows 命令解释程序定向到批处理程序
               中某个带标签的行。
GPRESULT       显示计算机或用户的组策略信息。
GRAFTABL       使 Windows 在图形模式下显示扩展
               字符集。
HELP           提供 Windows 命令的帮助信息。
ICACLS         显示、修改、备份或还原文件和
               目录的 ACL。
IF             在批处理程序中执行有条件的处理操作。
LABEL          创建、更改或删除磁盘的卷标。
MD             创建一个目录。
MKDIR          创建一个目录。
MKLINK         创建符号链接和硬链接
MODE           配置系统设备。
MORE           逐屏显示输出。
MOVE           将一个或多个文件从一个目录移动到另一个
               目录。
OPENFILES      显示远程用户为了文件共享而打开的文件。
PATH           为可执行文件显示或设置搜索路径。
PAUSE          暂停批处理文件的处理并显示消息。
POPD           还原通过 PUSHD 保存的当前目录的上一个
               值。
PRINT          打印一个文本文件。
PROMPT         更改 Windows 命令提示。
PUSHD          保存当前目录，然后对其进行更改。
RD             删除目录。
RECOVER        从损坏的或有缺陷的磁盘中恢复可读信息。
REM            记录批处理文件或 CONFIG.SYS 中的注释(批注)。
REN            重命名文件。
RENAME         重命名文件。
REPLACE        替换文件。
RMDIR          删除目录。
ROBOCOPY       复制文件和目录树的高级实用工具
SET            显示、设置或删除 Windows 环境变量。
SETLOCAL       开始本地化批处理文件中的环境更改。
SC             显示或配置服务(后台进程)。
SCHTASKS       安排在一台计算机上运行命令和程序。
SHIFT          调整批处理文件中可替换参数的位置。
SHUTDOWN       允许通过本地或远程方式正确关闭计算机。
SORT           对输入排序。
START          启动单独的窗口以运行指定的程序或命令。
SUBST          将路径与驱动器号关联。
SYSTEMINFO     显示计算机的特定属性和配置。
TASKLIST       显示包括服务在内的所有当前运行的任务。
TASKKILL       中止或停止正在运行的进程或应用程序。
TIME           显示或设置系统时间。
TITLE          设置 CMD.EXE 会话的窗口标题。
TREE           以图形方式显示驱动程序或路径的目录
               结构。
TYPE           显示文本文件的内容。
VER            显示 Windows 的版本。
VERIFY         告诉 Windows 是否进行验证，以确保文件
               正确写入磁盘。
VOL            显示磁盘卷标和序列号。
XCOPY          复制文件和目录树。
WMIC           在交互式命令 shell 中显示 WMI 信息。

有关工具的详细信息，请参阅联机帮助中的命令行参考。
```

# 基础
## 打印
``` bash
:: 这是一行注释
rem 这个也是一行注释
@echo off :: 关闭命令回显，也就是说，如果不关闭，那么bat脚本执行会把每条命令显示一遍
echo hello world
pause :: 执行结束后，需要按任意按键才关闭窗口
```
## 管道
``` bash
dir | find "hello" :: 也就是将前面的输出作为后面的输入
```
## 程序返回码
``` bash
每条程序都会有一个返回码 errorlevel
:: 两个%用来使用变量
echo %errorlevel% :: 这一条命令打印上一条程序的返回码
:: 默认值为0，也就是执行成功
:: 执行出错，一般为1
:: 中断执行会设置为负数
```
## 输出
使用<kbd>></kbd> <kbd>>></kbd>进行重定向
前者是覆盖，后者是追加
默认输出应该是你的终端界面，如果重定向到文件，怎会输出到文件中
`stdout`默认输出代码为1，`stderr`错误输出代码为2
``` bash
ping lmzyoyo.top > nul
ping lmzyoyo.top 1>null
::将默认输出重定向到nul，上面两条命令等价
ping lmzyoyo.top >output.log 2>&1
:: 将默认输出和错误输出均重定向到output.log
```
## title
设置cmd窗口标题
``` bash
title hello world
```
## color 
color [attr] 显示背景、前景色
``` bash
color 07 :: 前面是背景，后面是前景
color :: 显示默认颜色
0 = ⿊⾊ 8 = 灰⾊
1 = 蓝⾊ 9 = 淡蓝⾊
2 = 绿⾊ A = 淡绿⾊
3 = 湖蓝⾊ B = 淡浅绿⾊
4 = 红⾊ C = 淡红⾊
5 = 紫⾊ D = 淡紫⾊
6 = 黄⾊ E = 淡黄⾊
7 = ⽩⾊ F = 亮⽩⾊
```
## goto
```bash
if "%2" == "" goto nom ::直接goto
:nom ::使用单个冒号进行标注
```
## 变量
默认是字符串变量的概念，想要单独设置数字，则需要 /A
``` bash
set a=abc ::设置 a的变量为abc
set b="abc" :: 设置b的变量为"abc"

set d=1
set /A d=%d%+1
echo %d% :: 打印2

::更多set用法请看：
help set


set /P var=请输入
::那么var的值就是用户输入的
```

## 变量延迟
对于一行代码，bat会读取整行代码
```bash
@echo off
set a=4
set a=5 & echo %a% ::显示的是4，因为读取了整行代码，并且把之前执行的a=4代入了
```
克服这个问题，引入下列解决方式
```bash
@echo off
set i=10
setlocal ENABLEDELAYEDEXPANSION
set i=11 & echo !i!
```
再举一个例子
``` bash
@echo off
setlocal enabledelayedexpansion
for /l %%i in (1,1,5) do (
set a=%%i
echo !a!
)
pause
:: 打印 1 2 3 4 5，也就是说()里面是同一行命令
```
可以不解决这个特性，也就是到下一行命令才更新的特性写下面这个脚本，更换两个变量值
```bash
@echo off
::⽬的：交换两个变量的值，但是不使⽤临时变量
::Code by JM 2007-1-24 [email=CMD@XP]CMD@XP[/email]
::出处：http://www.cn-dos.net/forum/viewthread.php?tid=27078
set var1=abc
set var2=123
echo 交换前： var1=%var1% var2=%var2%
set var1=%var2%& set var2=%var1%
echo 交换后： var1=%var1% var2=%var2%
pause
```
## 特殊符号
- @：命令行回显屏蔽符
- %：批处理变量引导符号，如 %1 %2就是命令行传入的前两个参数，%0就是该执行文件的
- \>和>>：重定向符号，后面是追加，前面会覆盖
- <、>&、<& ：重定向符号
- \|：命令管道符
- \^：转义字符，可以行尾增加代表命令分行。也可以 ^& 代表 &
- &、&&、||：组合命令，第一个是不管执行是否成功，第二个是前面命令成功才执行后面的，第三个是前面命令执行成功就不执行后面的了
- ""：字符串界定符
- ,：逗号，有些地方可以替代空格
- ;：分号，`dir C:;D:;E:;`相当于`dir C:       dir D:      dir E:`
- ()：括号，一个括号内的命令被视为一条命令行
- !：感叹号
- \*、?：文件通配符，\*代表多个字符，?代表单个字符


# IF
## 基础版本
基础只有三种写法
- 判断`errorlevel`是否等于`number`，`not`可选
- 判断两字符串是否相等，如果字符串全是数字组成，那么将会比较两个数字
	- 如果字符串有空格，可以使用 `[]` `{}`包裹字符串，并且两个字符串要使用过相同的包裹符号
- 判断文件是否存在

```bash
IF [NOT] ERRORLEVEL number command
IF [NOT] string1 == string2 command
IF [NOT] EXIST filename command

IF .... ( :: 注意 ( 前面有一个空格
	command1
	command2
) ELSE (  :: 注意这一行的写法，一定要 ) <空格> ELSE <空格> ( 。并且一定要在一行
	command1
	command2
)

```
## 扩展
扩展版本也只有三种写法
- `/I`表示不区分大小写，同样可以用于上述 string1 == string2
	- compare-op: 
		- EQU：等于
		- NEQ：不等于
		- LSS：小于
		- LEQ：小于等于
		- GTR：大于
		- GEQ：大于等于
- CMDEXTVERSION：命令拓展版本
- DEFINED：是否定义环境变量
```bash
IF [/I] [NOT] string1 compare-op string2 command
IF [NOT] CMDEXTVERSION number command
IF [NOT] DEFINED variable command
```
# FOR
## 基础介绍
``` bash
for %variable in (set) DO command [command-parameters]
for /D %variable in (set) DO command [command-parameters]
for /R [[drive:]path] %variable in (set) DO command [command-parameters]
for /L %variable in (start,step,end) DO command [command-parameters]

for /F ["options"] %variable in (file-set) DO command [command-parameters]
for /F ["options"] %variable in ("string") DO command [command-parameters]
for /F ["options"] %variable in ('command') DO command [command-parameters]
```
## 参数进阶
- /D 代表后面set中命令是对文件夹的操作，如`for /d %%i in (c:\*) do echo %%i`，打印所有`C:`目录下所有文件夹，而不包括文件
- /R：递归显示所有的文件
- /L：产生一个列表，如：(1,1,5)，则产生(1,2,3,4,5)
- /F:
	- options:
		- eol=c：指一个行注释字符的结尾(就一个)，默认已经使用
		- skip=n：文件开始时忽略的行数
		- delims=xxx：分隔符集，用来替换空格和跳格默认分隔符集，注意不要乱空格，会代表空格也是空格符号
		- tokens=x,y,m-n：指定哪一个符号被传递到每个迭代的for变量中，会导致额外的变量名称分配，而不仅仅只有一个%%i。如果最后以\*结尾的话，那么就把最后一个后面所有符号分配给最后一个变量。
		- usebackq：正常是（file-set表示文件，不能有空格；单引号表示命令；双引号表示字符串）。加了之后就是（双引号表示文件，可以有空格；单引号表示字符串；后引号\`表示命令）
## 变量进阶
~I - 删除任何引号(")，扩展 %I
%~f I - 将 %I 扩展到⼀个完全合格的路径名
%~dI - 仅将 %I 扩展到⼀个驱动器号
%~pI - 仅将 %I 扩展到⼀个路径
%~nI - 仅将 %I 扩展到⼀个⽂件名
%~xI - 仅将 %I 扩展到⼀个⽂件扩展名
%~sI - 扩展的路径只含有短名
%~aI - 将 %I 扩展到⽂件的⽂件属性
%~tI - 将 %I 扩展到⽂件的⽇期/时间
%~zI - 将 %I 扩展到⽂件的⼤⼩
%~$PATH:I - 查找列在路径环境变量的⽬录，并将 %I 扩展
到找到的第⼀个完全合格的名称。如果环境变量名
未被定义，或者没有找到⽂件，此组合键会扩展到
空字符串
即：我们使用 %%~i表示删除任何引号的%%i

# 变量
为什么这里又将变量呢？这里的变量是预设变量
| 变量名称                                                     | 变量类型 | 变量作用                       |
| :-: | :-: | :-- 
|%ALLUSERSPROFILE% |本地 |返回“所有⽤户”配置⽂件的位置。|
|%APPDATA%| 本地| 返回默认情况下应⽤程序存储数据的位置。|
|%CD% |本地| 返回当前⽬录字符串。|
|%CMDCMDLINE% |本地| 返回⽤来启动当前的 Cmd.exe 的准确命令⾏。|
|%CMDEXTVERSION% |系统| 返回当前的“命令处理程序扩展”的版本号。|
|%COMPUTERNAME%| 系统 |返回计算机的名称|。
|%COMSPEC% |系统 |返回命令⾏解释器可执⾏程序的准确路径。|
|%DATE% |系统 |返回当前⽇期。使⽤与 date /t 命令相同的格式。由 Cmd.exe ⽣成。有关date 命令的详细信息，请参阅 Date。|
|%ERRORLEVEL%| 系统| 返回上⼀条命令的错误代码。通常⽤⾮零值表⽰错误。|
|%HOMEDRIVE% |系统| 返回连接到⽤户主⽬录的本地⼯作站驱动器号。基于主⽬录值⽽设置。⽤户主⽬录是在“本地⽤户和组”中指定的。|
|%HOMEPATH% |系统| 返回⽤户主⽬录的完整路径。基于主⽬录值⽽设置。⽤户主⽬录是在“本地⽤户和组”中指定的。
|%HOMESHARE% |系统 |返回⽤户的共享主⽬录的⽹络路径。基于主⽬录值⽽设置。⽤户主⽬录是在“本地⽤户和组”中指定的。|
|%LOGONSERVER% |本地| 返回验证当前登录会话的域控制器的名称。|
|%NUMBER_OF_PROCESSORS%| 系统| 指定安装在计算机上的处理器的数⽬。|
|%OS% |系统| 返回操作系统名称。Windows 2000 显⽰其操作系统为 Windows_NT。|
|%PATH% |系统| 指定可执⾏⽂件的搜索路径。|
|%PATHEXT% |系统| 返回操作系统认为可执⾏的⽂件扩展名的列表。|
|%PROCESSOR_ARCHITECTURE% |系统| 返回处理器的芯⽚体系结构。值：x86 或 IA64 基于Itanium|
|%PROCESSOR_IDENTFIER%| 系统| 返回处理器说明。|
|%PROCESSOR_LEVEL%| 系统| 返回计算机上安装的处理器的型号。|
|%PROCESSOR_REVISION% |系统| 返回处理器的版本号。|
|%PROMPT%| 本地| 返回当前解释程序的命令提⽰符设置。由 Cmd.exe ⽣成。|
|%RANDOM% |系统| 返回 0 到 32767 之间的任意⼗进制数字。由 Cmd.exe ⽣成。|
|%SYSTEMDRIVE%| 系统| 返回包含 Windows server operating system 根⽬录（即系统根⽬录）的驱动器。|
|%SYSTEMROOT% |系统| 返回 Windows server operating system 根⽬录的位置。|
|%TEMP% 和 %TMP% |系统和⽤户| 返回对当前登录⽤户可⽤的应⽤程序所使⽤的默认临时⽬录。有些应⽤程序需要 TEMP，⽽其他应⽤程序则需要 TMP。|
|%TIME%| 系统| 返回当前时间。使⽤与 time /t 命令相同的格式。由 Cmd.exe ⽣成。有关time 命令的详细信息，请参阅 Time。|
|%USERDOMAIN% |本地| 返回包含⽤户帐户的域的名称。|
|%USERNAME%| 本地| 返回当前登录的⽤户的名称。|
|%USERPROFILE%| 本地 |返回当前⽤户的配置⽂件的位置。|
|%WINDIR% |系统| 返回操作系统⽬录的位置。|

# 时间
``` bash
echo %date% %time%
:: 2023/04/16 周日 17:00:20.46

echo %date:~x,y%
::表示从x位置开始，接下来往后的y个字节数据，注意时间在1点到9点的特殊情况
echo %date:~0,4% :: 2023
echo %date:~11,2% :: 周日
```


