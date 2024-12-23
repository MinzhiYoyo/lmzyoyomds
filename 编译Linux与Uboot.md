---
title: 编译Linux与Uboot
id: 8
date: 2024-12-03 17:09:45
auther: yutanbird
cover: http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141843271749.png
excerpt: 后期因为考研的原因，只进行到了编译根文件系统，uboot以及Linux都已经成功启动，但还未记录在此
permalink: /archives/%E7%BC%96%E8%AF%91linux%E4%B8%8Euboot
categories:
 - linux
 - comb
tags: 
---



# 一、前言

目前没有前言

# 二、准备工作

准备太久了

# 三、PCB绘制

不是我绘制的

# 四、下载编译工具链

我下的是`arm-linux-gnueabi-`

# 五、u-boot移植

## 1、下载`u-boot`

``` shell
git clone https://github.com/Lichee-Pi/u-boot.git
git branch -a  # 查看分支，我们使用的是nano-v2018.01u-boot
git checkout nano-v2018.01u-boot  # 切换到nano-v2018.01u-boot这个分支
```

## 2、选取`config`配置

考虑到水平有限，我们采用从现有的配置上进行更改。或者，我完全不进行更改，就直接使用了。

### *对几个config简单介绍：*

>`f1c100s_nano_uboot_defconfig`：`SPI Flash`支持版
>
>`licheepi_nano_defconfig`：不带`SPI Flash`，从`TF`卡启动
>
>`licheepi_nano_spiflash_defconfig`：从`SPI`设备启动

其实上面三种我也分不清楚，我第一次采用的是 `licheepi_nano_defconfig`。我也找不到 `f1c100s_nano_uboot_defconfig`，但是在[这](https://gitee.com/LicheePiNano/u-boot)可以找到，这个是荔枝派的 `u-boot`，可能毕竟这款 `f1c100s`是国产芯片，在`GitHub`上面找不到。

### 选取`config`操作

``` shell
cd ~/u-boot  # 切换到 下载的 u-boot 的根目录
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- licheepi_nano_defconfig  
# 参数介绍：
# ARCH=arm 是arm架构的
# CROSS_COMPILE=arm-linux-gnueabi-  是之前的下载的编译工具链
# licheepi_nano_defconfig 选取的默认config
```

## 3、可视化配置

### 打开菜单命令

``` shell
make ARCH=arm menuconfig
# 之后上下键进行移动
# 空格或者回车进行选择
# 左右可以选择下方菜单
# ctrl+退格才能删除已经填入的默认参数
```

### 参数讲解

注意下面这两个参数就行

![image-20220305195147976](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141844131100.png)

#### `boot arguments`

> - `console=ttyS0,115200 panic=5 rootwait root=/dev/mmcblk0p2 earlyprintk rw`
>
> > **`console=ttyS0,115200`** 表示终端为ttyS0即串口0,波特率为115200；
> >
> > **`panic=5` **字面意思是恐慌，即linux内核恐慌，其实就是linux不知道怎么执行了，此时内核就需要做一些相关的处理，这里的5表示超时时间，当Linux卡住5秒后仍未成功就会执行Linux恐慌异常的一些操作。
> >
> > `rootwait` 该参数是告诉内核挂在文件系统之前需要先加载相关驱动，这样做的目的是防止因mmc驱动还未加载就开始挂载驱动而导致文件系统挂载失败，所以一般bootargs中都要加上这个参数。
> >
> > **root=/dev/mmcblk0p2** 表示根文件系统的位置在mmc的0:2分区处，**/dev**是设备文件夹，内核在加载mmc中的时候就会在根文件系统中生成**mmcblk0p2**设备文件，这个设备文件其实就是mmc的0:2分区(这里对应TF卡的第二个分区：rootfs)，这样内核对文件系统的读写操作方式本质上就是读写/dev/mmcblk0p2该设备文件。
> >
> > **`earlyprintk`** 参数是指在内核加载的过程中打印输出信息，这样内核在加载的时候终端就会输出相应的启动信息。rw表示文件系统的操作属性，此处rw表示可读可写。

#### `bootcmd`

> - `load mmc 0:1 0x80008000 zImage;load mmc 0:1 0x80c08000 suniv-f1c100s-licheepi-nano.dtb;bootz 0x80008000 - 0x80c08000;`
>
> > ![image-20220305195531843](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141844720138.png)



## 、遇到问题不要慌

### （1）` execute 'swig'`

> 没有`swig`这个东西，安装即可：`sudo apt install swig`
>
> `swig`：我从[网上](https://zh.wikipedia.org/wiki/SWIG)了解到是一个将`C/C++`的类封装成库，给`Python、Lua、PHP`等脚本语言调用。（题外话）

## 、参考链接

> [参考博客](https://cnblogs.com/twzy/p/14865952.html)
>
> [参考荔枝派](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141846763856.png)

# 六、`Linux`的移植

## 1、分区操作

​	一个硬盘对于`Linux`来说，需要进行**挂载**命令是：`mount`，一般插上自动挂载），然后再能使用，然后需要**卸载**（命令：`umount`）才能操作，如格式化，分区之类的。我们借助了`Ubuntu`下图形化工具——**`Gparted`**，命令行进行下载就行了。

​	然后，买到手的`sd`卡，可能已经分区了，有一个主分区。如果没法删除，或者进场出现例如*`/dev/sdb contains a mounted filesystem`*，我的建议，使用一个全0的映像文件写入`sd`卡，将前面的数据覆盖。

​	之后便是分区了，将只有一个未分区的，且没有任何分区的`sd`卡连接`Ubuntu`。

### 分区1、`boot`

> 新建一个分区
>
> 之前剩余空间为**1M**，新大小为**32M**，文件系统为**`fat16`**，卷标就设置为**`boot`**
>
> 对于之前的`1M`，是留给**`uboot`**的，而且在`gparted`是看不到的
>
> **图片来源于网络**
>
> ![image-20220314221728714](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141847334316.png)

### 分区2、`rootfs`

> 新建一个分区
>
> 之前剩余空间为**0M**，新大小为**100M**，文件系统为**`ext4`**，卷标就设置为**`rootfs`**
>
> ***图片来源于网络***
>
> ![image-20220314221807562](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141848448533.png)

### 最后的效果

![image-20220314221850652](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141849061332.png)

## 2、编译 `Linux`

### 更改设备树

# 七、编译`rootfs`