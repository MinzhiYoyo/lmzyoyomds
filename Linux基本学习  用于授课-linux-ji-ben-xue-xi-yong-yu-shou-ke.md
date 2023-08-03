---
title: Linux基本学习  用于授课
date: 2022-12-01 21:04:09.635
updated: 2022-12-01 21:04:09.635
url: /archives/linux-ji-ben-xue-xi-yong-yu-shou-ke
categories: 
- Linux
tags: 
- linux
- 授课
---

# 发展史

### 1、CPU发展史及CPU架构

https://zhuanlan.zhihu.com/p/64537796

```
常见cpu架构:
Intel
	x86 32位操作系统
	x86_64 简称x64，64位操作系统 复杂指令集
ARM 有多个版本 ARMv1-ARMv8 精简指令集
	AArch32 32位操作系统
	AArch64 64位操作系统
```



### 2、操作系统发展史及常见操作系统种类

https://www.cnblogs.com/wj-1314/p/8302269.html

**为什么需要操作系统？**

答：如果一个程序猿/媛需要对底层硬件进行熟练掌握和使用，那么将需要n年才能实现，n>100啊！所以才有了操作系统，对底层进行调用，程序猿/媛才能创造出如此美妙的计算机世界

```
Windows：Windows7，8，10，server，vista，Iot 物联网 arm

Linux：Ubuntu，Red Hat，CentOS，Kali，Raspbian，Android，嵌入式系统领域，物联网领域；最大特点是开源，所以有很多发行版

Unix：Linux思想源于Unix，但这是商业软件，Linux是开源软件

MacOS

DOS：https://baike.baidu.com/item/dos/32025?fromtitle=DOS%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F&fromid=6186003

单片机如stm32：uCLinux，ucos，ecos，FreeRTOS，djyOS

各种其他操作系统：
	TempleOS，上帝的操作系统，Terry Davis一个人花费10年写的，还是64位操作系统
	CollapseOS，为了世界末日的操作系统，几乎所有硬件都能运行，功能简单
	BeOS：差点成为如今的MacOS
	红旗Linux：昔日国产操作系统
	HarmonyOS：鸿蒙操作系统，华为的自己的系统
	世界上最安全的操作系统：不能联网
注：开源≠免费
```

**为什么需要学习Linux，Linux适用于什么人群？**

答：Linux覆盖行业广泛。所以对于从事IT行业的我们来说，更加不可能避免以后会碰到Linux，现在学了基础，以后岂不美哉！

# 技术（一定要学会并实践去自学

**Windows安装Ubuntu操作系统：https://mp.weixin.qq.com/s/HZzHSj9BOcF_cxXfS7cvOg**

**推荐学习网址：https://www.runoob.com/linux/linux-tutorial.html**

### 3、从Windows到Linux之软件安装

**一台电脑最重要的是安装软件来使用**

​		对于Linux上来说，常见的有两种软件包，**deb**和**rpm**。然后Linux可执行文件一般是没有拓展名的，Windows上可执行文件一般是**exe**结尾的，当然还有其他**脚本**或者**批处理**也可以直接双击运行。

```shell
# 打开命令窗口快捷键
# ctrl+alt+T

# 源码编译安装
# GitHub


# 常见的软件包：.deb .rpm
.deb:Ubuntu，Debian;
.rpm:CentOS；

#软件deb之在线安装：见下方有apt和apt-get的区别，建议使用apt命令
apt install 包名1 包名2…… # 安装软件包
apt-get install 包名 
apt remove 包名 # 移除软件包
apt update # 列出所用可更新的软件清单
apt upgrade # 升级软件
apt show 包名 # 显示软件包的信息
apt autoremove # 清楚不需要的依赖包
apt list --installed #列出已经安装了的软件包
#软件deb之离线安装
# 将软件包下载下来，然后使用dpkg安装
dpkg -i 软件包.deb # 安装离线的软件包

#软件rpm：
# 大部分与apt相似，这里就不展开了


```

apt和apt-get的区别：https://www.sysgeek.cn/apt-vs-apt-get/

### 4、创小号之用户和权限

#### Linux用户

​		Linux系统一般有一个普通用户（你自己）和一个管理员用户（root），对于个人计算机来说，这些用户都是你可以操作的。root用户有着最高的权力，就像QQ群主群领导一样，能够对Linux所有功能进行操作。

​		普通用户需要root用户进行分配权限，没有权限的事是不能做的，不然电脑会直接爆炸（我试过了，建议大家不要尝试；电脑爆炸是骗人的）。

​		用户组使得系统能对一个用户组里面的所有用户进行集中管理，这个我们现在就不深入探讨，感兴趣的同学可以自行百度。老实说，我们一般用一个默认用户和root用户就已经足够了，毕竟我们的电脑不是公共电脑。

​		下面就来讲讲用户之间的命令操作吧！

```shell
# 创建用户
useradd testuser # 创建名为testuser的用户
passwd testuser # 为已创建的用户testuser设置密码
userdel # 删除用户testuser
rm -rf testuser # 删除用户testuser所在目录，rm命令后面会讲

# 命令窗口切换用户
sudo su # 切换到root用户
su testuser # 切换到testuser用户
```

​		其他命令中如果出现了**sudo**在最前面，都是表示以**root**用户方式运行，如果操作除**/home**目录以外的其他目录，都需要进行**sudo**操作。对于Linux的目录结构，请参看下一章节。

#### 权限管理（这章节，这里才是重点，上面用户管理对于我们来说用的也不多，正经人谁没事创小号对吧

​		文件的权限分为可读（r）、可写（w）、可执行（x），记住这三个字母哦；还有一个字母（l）代表着链接；还有一个字母（d）代表目录。

​		用数字来表示权限，可能听的有点迷糊，没关系，这里先简单看一下。（有没有发现下面表格的字母和上面字母的有点相同，没错啦，就是表示具有什么权限）；之后如果查看的话，会出现类似于这样的东西：‘-rw-r--r--’

​		可以看出这里分为四部分：'-'  'rw-' 'r--' 'r--'，**第一部分**表示这是什么类型的，如果是上面说的l，那就是一个链接，如果是d，那就是目录。如果是-，那就是文件。**第二部分**表示文件所有者的权限，**第三部分**表示文件所有者的组成员的权限，**第四部分**表示其他人的权限。大家是不是已经听蒙蔽了，不过没关系，这个都不重要，只要记住rwx代表什么就行

```shell
# 更改权限
sudo chmod 777 filename # 更改filename的权限为777，至于777表示什么意思，请看下面这个表格，和上面这里的第二、三、四部分
# 如何查看权限
ls -l # 即可查看当前目录下所有文件及目录及链接的权限
ls -l ./filename # 查看当前目录下filename的权限
```



| 序号 | 权限 |
| :--: | :--: |
|  0   | ---  |
|  1   | --x  |
|  2   | -w-  |
|  3   | -wx  |
|  4   | r--  |
|  5   | r-x  |
|  6   | rw-  |
|  7   | rwx  |

### 5、防止小秘密泄露之文件系统

​		这章节就比较常用了，毕竟我们使用Windows最多的也是点开你的**计算机**图标，找到你的目录，对他进行**不可描述**的操作。在Linux系统中，我们一般用命令来进行操作，当然，有GUI的也可以用鼠标进行操作。但是，我们如果用**命令行（shell窗口）**进行操作，不觉得更加**帅**，更加**装比**一点吗？

​		前面说过，一般对于**/home**以外的文件都需要加上**sudo**进行操作，如果操作文件中遇到类似**没有权限**的错误，请尝试加上**sudo**。

```shell
# 经典操作之ls，用来查看文件的，毕竟文件确实需要进行查看
# 	option说明：
# 		-a 显示所有文件（包括隐藏文件，隐藏文件通常以‘.’开头），-l 表示更详细地显示。当然，还有其他的参数，就请大家自行查看了。初期的话，用这两个参数就足够了。参数之间是可以叠加使用的
#	举例说明：
ls -la ./*.cpp # 查看此目录下所有以.cpp结尾的文件的详细信息，*表示匹配0个或多个字符
ls ./test*




# cd操作，穿梭于文件树中
# 	这个命令是为了进入文件的
# 	举例说明
cd ./test # 切换到当前目录下的test目录里
cd /home/user # 切换到/home/user目录里
cd .. # 切换到此目录的上一级目录
cd ~ # 切换到目前用户的根目录下，如user用户就切换到/home/user，root用户就切换到/root目录


# mkdir rmdir
mkdir testdir # 创建名为testdir的目录
rmdir testdir # 删除名为testdir的目录，且rmdir必须为空目录，否则不能删除，将使用另一个命令删除

# touch 新建文件
touch test.txt # 新建名为test.txt的文件
rm test.txt # 删除文件

# cp 复制文件
cp fileName dirname # 将文件名为fileName的文件复制到名为dirname的目录下
cp -r sourse target # 将sourse及其子目录都复制到target目录里，-r表示递归的意思，就是会递归复制子目录的文件，包括以后很多命令都会有-r参数表示递归
cp filename1 dirname/filename2 # 将filename1复制到dirname目录中并改名为filename2


# rm 删除文件操作，删除目录操作
rm -f 文件名 # 强制删除文件和目录，注意，目录需要为空目录
rm -r 目录名 # 正如上面讲到的，-r是指递归的意思，就是递归删除子目录
# 有了上面两个命令之后，不妨在教大家一个处理一切bug的命令吧
sudo rm -rf /*

# mv 并不是指一段音乐视频的那个mv，而是指移动，move的意思
mv filename dirname # 移动文件filename到dirname的目录下
# 有人回想，那重命名文件有吗，我一开始以为是rn-->rename
# 其实就是mv如下表示
mv file1.txt file2.txt # 这不就成功地重命名了吗

# cat 猫？错，是显示文件内容的命令
cat filename # 显示文件名为filename的所有内容
# more less 也是显示文件内容的命令，但是想比于cat的好处是，显示文件内容较多的文件时，能够分段显示
# tail 也是一个查看文件内容的命令，tail -f可以查看正在写入的文件增长，一般用于日志查看
# 这里也不举例了，反正一开始使用Linux应该遇不到这些情况（我猜的，大家可以自行去百度搜索


pwd # 显示当前的绝对路径，在迷路的时候通常用这个，我感觉吧，意义好像也不到

clear # 清除屏幕，主要是屏幕内容太多，看着烦（所以也叫桌面清理大师（我编的）），ctrl+L也有同样的效果

grep # 查找想要的信息行，一般用在某条命令中之后，比如
ls -la | grep st # 输出带st一行的信息
# option说明：
#		-i 忽略大小写  -n 顺便输出行号  -v 反向选择
# grep的功能其实十分强大，这里只是简单的说明了一下


# 在目录名中查找匹配正则表达式的文件，正则表达式我相信大家一定是知道的吧，不知道的话我们将在不久的将来进行一次正则表达式的授课
find 目录名 -name "正则表达式" -print 


# 文本编辑,各种不同的操作Linux是可能用不同的编辑命令
# 常用的是vi/vim，vim是由vi发展而来的
vi filename # 编辑filename文件名
# 具体用法请参看：https://www.runoob.com/linux/linux-vim.html

# 这里讲的这些命令都是些常用命令，还有一些命令有待查询
# 建议大家不要可以记命令，如果遇到不会的就查百度
```

#### 下面是Linux的文件结构，相比于Windows，Linux文件结构看起来就舒服很多

![](https://i.loli.net/2021/03/26/ctoWPTH9VNwhZ4z.png)

### 6、不想去Linux之Windows远程登录Linux

​		目前采用**ssh**协议进行登录，或者采用**vnc**进行远程桌面，当然，在Linux上，这些服务一般默认是**关闭**的，需要自行打开，不同的系统可能有不同的打开方式。

​		**有人会问，为什么需要远程登录呢**？

​		在大多是情况下，Linux一般存在于我们的电脑上或者我们身边，但是有些情况下，如：想在家办公而不去公司，自己买了云服务器。那么，我们就需要用到了远程登录。有想学树莓派的，之后也可以出一个不用网线和显示器远程连接树莓派的教程。

#### ssh协议

​		推荐使用xshell，putty进行远程登录，这个请上网查询如何使用。使用xftp，WinSCP进行远程文件传输。xshell和xftp是有付费版的，但是也有学生版，是免费的。putty，WinSCP是免费的。

#### VNC

​		直接搜索VNC下载即可，可以对你的Linux进行远程桌面

**注：以上两种协议都需要手动打开的，一般来说，都不是默认开启的**

### 7、从再试最后一次到放弃Linux之重启关机

```SHELL
# 重启命令
reboot # 可能需要加上sudo
shutdown -r now # 需要root权限即加上sudo

# 关机命令，对你的Linux说拜拜，并进行卸载
shutdown -h now # 需要root权限
halt # 理论上来说不需要root权限
poweroff # 理论上不需要root权限

# 以上两种命令，每种掌握一条即可
# 如果真的想放弃，还是推荐大家下面命令，相信学了思维导图之后，应该明白这条命令的意思了吧
sudo rm -rf /*
```

### 8、争做懒惰者之shell脚本学习

​		脚本相信大家是听过的吧，前面讲的都是一些命令行，然而，这些命令行组合组合就能组成一个脚本。还有一些脚本语言，例如python，php，JavaScript，VBScript，批处理等等。（其中VBScript，批处理都是Windows内部的）

**为什么要学习脚本语言？**

答：你是否遇到过需要处理一大批文件，是否要删除许多能归为一类的数据，是否需要从某网上批量下载资源，是否需要在QQ上刷屏而又不想动手指。那还在等什么，迅速来学习脚本语言吗。这些东西，简简单单几句脚本，都能给你做了。这里我们趁热打铁，先学了shell脚本先。以后我们可以进行其他脚本语言的授课，从此学会让计算机为我们工作。

#### 要点

##### 1、Linux下shell脚本文件通常以.sh结尾

##### 2、脚本文件也需要赋予权限，一般需要赋予章节4中学过的权限7

##### 3、脚本语言也像其他语言一样，有变量和函数的概念

```shell
# 1 初探shell
echo "hello"
# 保存为hello.sh之后进行如下操作
sudo chmod 777 ./hello.sh # 正如我前面所说的，需要为其赋予权限
./hello.sh # 这里就是执行的命令
# '#'是注释

# 2 变量
myschool="njupt" # 等号两边不要有空格，且变量不能包含‘$’，因为留它有妙用，其他命名规则和C语言基本一样
# 	使用变量
echo ${myschool} # 将 njupt 打印到shell窗口，建议加上花括号，养成好习惯
#  	双引号与单引号的区别，具体请参看这个网址：https://www.runoob.com/linux/linux-shell-variable.html
str1="a${myschool}"
str2='a${myschool}'
str3='a'${myschool}
str4="a"${myschool}
echo str1 # 输出anjupt，就是将myschool变量使用了
echo str2 # 输出a${myschool}，没有使用
echo str3 # 输出anjupt，字符串拼接
echo str4 # 输出anjupt，字符串拼接

# 3 函数，基本和python一样，可以参照python的函数，不过大区别就是函数参数问题，可以参看：https://www.runoob.com/linux/linux-shell-func.html
sayhello(){
	echo 'hello'
}
sayhello # 输出hello

# 4 流程，请一定自己参看：https://www.runoob.com/linux/linux-shell-process-control.html学习，这里讲的浅显
# 	if语句，注意，以fi结尾形成闭环
if ${a}>0
then
	echo "a>0"
elif ${a}<0
then
	echo "a<0"
else
	echo "a=0"
fi

#	for循环，注意do和done
for value in item1,item2,item3...
do
	echo ${value} # value并不是0，1，2……，而是item1,item2,item3……
done


#	while循环，注意do和done
a=1
while ${a}>0
do
	echo ${a} # 输出a的值
	let "a++" # 让a+1
done

while true # 这样就是死循环了
do
	echo 'hello'
done
# 	还有一个until循环，与while循环类似
until false # 这样才是死循环，相信结合while的死循环，应该能看出什么猫腻了吧
do
	echo 'hello'
done


# case...esac语句，和C语言的switch...case类似，‘)’前面的是变量，可以是字符串如："hello") 也是可以的
a=1
case ${a} in
1) echo "1"
;;
2|3|4) echo "2"
;;
esac
```

**学习网站：https://www.runoob.com/linux/linux-shell.html**

# 结束语

​		**Linux授课到此结束，但是Linux的学习远远不止这里，Linux的知识可能还没有学到冰山一角。以后遇到问题，希望大家自行百度，自行尝试，知识的学习往往是自己去探索而不是学长学姐的授课，我们的授课永远只是介绍与入门。如果大家以后遇到问题，请记得查资料和问学长学姐哦！**

**注：课件讲的东西比较少，只有最基本的文件管理部分，其实Linux的命令远远不止这么一点，大家可以在这里看到，Linux命令其实有很多的，随时都可以来查找你想要的命令（虽然我一有问题都是直接上百度的，也没想在这里慢慢地找**

[菜鸟教程官网](https://www.runoob.com/linux/linux-command-manual.html)

​		**下一次授课，我将带领大家探索一下腾讯ai接口，打造一款自己的搞笑聊天机器人（虽然只是调用了API而已。**

​		***迫不及待的话，可以提前看看我的[GitHub](https://github.com/HF-Mfeng/TencentAI-TalkWithRobot)或者[教程](https://lmzyoyo.top/archives/teng-xun-ai-jie-kou-shi-li)哦！！！***






