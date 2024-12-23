---
title: Android刷机
id: 86
date: 2024-12-03 17:09:55
auther: yutanbird
cover: 
excerpt: 好好好，希望大家都变砖。
permalink: /archives/androidflashing
categories:
 - comb
tags: 
 - android
---



# 引言

&emsp;这里没有引言，参考[极客湾](https://www.bilibili.com/video/BV1BY4y1H7Mc)。好，不是参考，是记录，嘿嘿。

fastboot刷机：线刷；Recovery刷机：卡刷。

# 权限

- 安卓系统一共有三种权限，依次递增：
    - 第三方软件
    - 用户权限
    - root权限

一般手机常说root就是提升到root权限。

# 安卓分区

- 安卓分区包括如下几个：

    - Boot分区：存放启动与引导文件，包括操作系统的`Kernel`(内核)和`Ramdisk`(虚拟内存)

        > root其实就是在修改boot分区。改CPU调度，CPU超频都需要操作该分区。
        >
        > 如果该分区损坏，手机会卡在logo界面。

    - System分区：包括操作系统和预装软件

        >  升级或者刷机都是在操作该分区
        >
        > 如果该分区损坏，会卡在开机第二屏，也就是各大厂商的UI界面（如MIUI界面）
        >
        > - Vendor分区：包括定制软件和库文件，方便后期维护，目前大多存在于System分区内部

    - Data分区：用户数据，包括应用、媒体、文档、系统设置等等

    - Cache分区：缓存分区可以帮助你快速打开各个软件数据，清除了之后，再次打开软件就会有了的。

    - Recovery分区：恢复、更新、擦除和重启其他分区等等，类似于Windows PE，刷机刷系统都是在这

        > 值得注意的是，目前大多数的Recovery分区不存在了，在安卓7之后，Recovery的功能被A/B System Updates模式替代，也相当于Recovery并入到了Boot里面
        >
        > - A/B System Updates：把Boot和System分区复制一份，平常用的叫做主分区（Slot A），另外一个叫做备用分区（Slot B）
        >
        >     > 用户可以一边使用手机，一边升级系统（也就是更新Boot和System），然后重启把B分区变成A分区，A分区变成B分区。
        >     >
        >     > 还可以防止升级失败导致卡机
        >
        > - VAB分区：虚拟AB分区，相当于是AB的升级，安卓11推出
        >
        >     > 也就是，如果AB分区中不同的文件，那么就存在于原来的AB分区。如果是相同的文件，那么就存在于VAB分区，节省了重复文件导致的空间浪费。

# Fast Boot

Recovery是相当于Windows PE的，`bootloader`相当于电脑的`bios/uefi`了，当接通电源开机时，会初始化硬件设备，引导操作系统内核（Boot分区）等等。在引导阶段的后期，会进入一个叫做`fastboot`的阶段，常在这个阶段刷机。

在`fastboot`下，我们可以线接电脑，解锁设备、注入boot、线刷系统（还有一种叫做卡刷，就是把要刷的包拷贝进手机）等等。下面是不同手机进入`fastboot`的方式，首先关机，然后按按键

|      | 电源键 | 音量- | 音量+ | 是否需要数据线接电脑 |
| :--: | :----: | :---: | :---: | :------------------: |
| 华为 |        |   ●   |       |          ●           |
| 小米 |   ●    |   ●   |       |                      |
| OPPO |   ●    |   ●   |       |                      |
| VIVO |   ●    |       |   ●   |                      |
| 一加 |   ●    |   ●   |   ●   |                      |
| 魅族 |   ●    |   ●   |       |                      |
| 索尼 |        |       |   ●   |          ●           |
| 三星 |        |   ●   |   ●   |          ●           |

电脑端需要下载 Android SDK，在 https://developer.android.google.cn/studio/releases/platform-tools，解压后有个`fastboot.exe`文件，然后手机进入`fastboot`之后，在电脑打开`cmd`。

```sh
fastboot devices # 检测设备

# 如果检测不到设备，原因之一是没驱动
# 在 设备管理器-其他设备中找到一个叫做Android的设备，为其装上驱动即可。
# 也有可能是其他原因


## 下面是常用命令 ##

# 获取手机相关信息
fastboot getvar all

# 重启手机
fastboot reboot

# 重启到bootloader
fastboot reboot-bootloader

# 擦除分区
# fastboot erase (分区名)
# 例：清除system分区：fastboot erase system

# 刷入分区
fastboot flash (分区名) (分区镜像)
# 例：将boot镜像 "boot.img" 刷入boot分区：fastboot flash boot boot.img

# 引导启动镜像
fastboot boot (分区镜像)
# 例：启动到recovery分区：fastboot boot recovery.img

# 刷入ROM
fastboot update (刷机包)
# 例：将 update.zip 刷入：fastboot update update.zip

# 解锁Bootloader
fastboot oem unlock (参数见视频，视机型而定)

# 多设备使用
fastboot -s (命令)
# 通过fastboot devices获取序列号，控制多设备中的一个
# 例：清除序列号为'abc'设备的system分区：fastboot -s abc erase system
```

进入开发者选项，点击若干次版本号即可，然后打开ADB调试。（ADB调试有很多工具箱可以用，比如搞机工具箱或者秋之盒等等）。

# 解锁Bootloader

&emsp;首先，需要打开OEM解锁，在开发者选项中打开这个选项。有就打开，没有就算了。

&emsp;然后，需要进行root，下面需要分品牌讨论。（除了魅族之外，都需要先解锁Bootloader才能root，修改Boot分区，刷入`Magisk`。**解锁BL失去保修**）

|    品牌     | 方法                                                         |
| :---------: | ------------------------------------------------------------ |
|    魅族     | 打开[魅族官网](https://mroot.flyme.cn)下载root权限软件，复制PSN和CHIPID，选择绑定机型，在网站上填好信息即可。 |
|    小米     | 1.进开发者选项查看解锁状态；2.绑定账号设备；3.进入fastboot；4.电脑下载[官方工具](https://www.miui.com/unlock)；5.登录后连接手机，按工具内操作 |
|    一加     | 1.进入fastboot；2.电脑输入`fastboot oem unlock`；3.在手机上确认 |
|    三星     | 1.关机后同时按音量+-并插上电脑；2.根据提示                   |
|    索尼     | 1.拨号盘输入`*#*#7378423#*#*`；2.service info-configuration；3.查看bootloader unlock allowed，并记住IMEI；4.打开[索尼网站](https://developer.sony.com/develop/open-devices/get-started/unlock-bootloader)选择机型，输入IMEI，记住解锁码；5.fastboot模式下，电脑输入`fastboot oem unlock 0x_____`（这里填入解锁码） |
|    MOTO     | 1.fastboot模式下，电脑输入`fastboot oem get_unlock_data`；2.会返回几行数据，将所有数据首位拼接；3.登录[官网解锁页面](https://motorola-global-portal.custhelp.com/app/standalone/bootloader/unlock-your-device-a)填入代码，验证通过后邮箱发送解锁码；4.fastboot模式下，电脑输入`fastboot oem unlock <解锁码>` |
| oppo/realme | 1.进入[oppo](https://www.oppo.cn/thread-397164526-1)/[realme](https://www.realmebbs.com/post-details/1275426081138028544)后查看你的机型；2.在页面底部下载对应app，在app提交申请；（可以在官网查看要求） |

因此，如果想玩机，推荐小米和一加。许多论坛都有资源，如国内的酷安或者国外的xda论坛。

# root

&emsp;前面有讲到root其实就是更改boot分区，更改boot分区就需要使用Recovery，所以接下来分两种方式进行root

## root方式1

&emsp;类似于电脑需要用一个PE来刷入新系统，所以我们需要一个Recovery来刷入新系统。但是，官方的Recovery的话，功能受限，所以需要一个第三方的Recovery，比如最著名的是：[TWRP](https://twrp.me/)(Team Win Recovery Project)。在TWRP下载对应固件，或者在论坛找魔改固件。

&emsp;fastboot模式下，电脑输入`fastboot flash recovery xxxx.img`（xxx.img是你下载的固件名字）。刷入完成后，如何进入Recovery呢？一是通过fastboot直接进入Recovery；二是开机后，通过adb命令(`adb reboot recovery`)进入，比较方便；三是fastboot模式下，通过adb命令(`adb reboot bootloader`)进入，也比较方便；四是查看下表。

| 品牌 | 电源键 | 音量- | 音量+ |                   其他                   |
| :--: | :----: | :---: | :---: | :--------------------------------------: |
| 小米 |   ●    |       |   ●   |                                          |
| OPPO |   ●    |   ●   |       |                                          |
| 一加 |   ●    |   ●   |       |                                          |
| 索尼 |   ●    |       |   ●   |                                          |
| 三星 |   ●    |       |   ●   | bixby键（如果有）、数据线（OneUI 3以后） |

&emsp;前面是root的准备工作，现在开始root。首先先接刷一款工具——[Magisk](https://zh.wikipedia.org/zh-tw/Magisk)，这是一个root管理工具。把这个包(一般是zip文件)放入手机里，然后在TWRP中install这个包，然后重启。再把刚刚的包改成apk，即可安装这个管理软件。打开Magisk后，如果显示版本，并且底部栏有超级用户选项。就代表root成功。

## root方式2

&emsp;现在有一个更简单的方法，那就是直接刷入boot镜像。流程就是，提取当前的boot镜像，然后由Magisk APP修补之后成为新的boot，再用命令刷入Boot（`fastboot flash boot BOOT.img`）。

&emsp;这个问题在于，你需要先root才能提取boot镜像，但是我们就是为了root才要提取镜像，现在陷入死循环了。方法之一就是，求助万能的网友，下载网上的boot镜像，也就是下载刷机包/线刷包，有些品牌如一加，官网就有对应刷机包。小米红米的话，可以去[xiaomirom.com](https://xiaomirom.com)这个网站下载；方法之二就是，可以在手机更新那里下载完成安装包，这样就可以把完整镜像下载在Download文件夹里。

&emsp;假设你搞到了线刷包（解压后有boot.img），或者搞到卡刷包（解压后有payload.bin文件，使用[payload_dumper](https://github.com/vm03/payload_dumper)这个文件解包，解包后找到boot.img），或者是直接就有boot.img。将你获得的boot.img文件传到手机，然后手机上先装好Magisk，打开之后，点击安装-选择修补一个文件-选择boot.img文件，等待修补完成。可以在Download文件夹找到修补后的boot文件，然后拷贝到电脑上，fastboot模式下刷入boot即可（`fastboot flash boot`）。

# Magisk更多玩法

&emsp;可以更改系统字体，更改音效（ViperFX），刷入性能控制脚本续航控制脚本，修改CPU调度，降压超频，修改游戏分辨率（GLTools）等等功能。

&emsp;下面来讲讲Magisk原理，它是在挂载一个跟System文件相隔离的系统来加载自己的内容，一切都在bootloader阶段就完成了，所以实现了功能，但是系统毫发无损。它不对System分区修改，所以它可以隐藏root，其他软件会检测不到root，这些软件可以正常工作。

# 救砖刷机

## 常规救

&emsp;如果进不了Recovery，那么就只能通过线刷来救砖了，就是得在fastboot下进行救砖。有些厂商有自己的线刷工具，如下表

|   品牌    |                           刷机工具                           |     刷机模式     |
| :-------: | :----------------------------------------------------------: | :--------------: |
|   小米    |            [Miflash](https://miuiver.com/miflash)            |     Fastboot     |
| OPPO/一加 |              [QFIL/MSM](https://qfiltoos.com/)               |       9008       |
|  realme   | [realme flash](https://www.realmebbs.com/post-details/1271082970484060160) |     Fastboot     |
|   索尼    | [Flashtool/Newflasher](https://forum.xda-developers.com/t/tool-newflasher-xperia-command-line-flasher.3619426/) |   SOMC强刷模式   |
|   三星    |              [Odin3](https://odindownload.com/)              | Download挖煤模式 |

&emsp;救砖流程就是，拿小米举例子：下载小米线刷包-进入fastboot连接电脑-miflash重刷系统即可。

## 更底层的救

9008，记住这个数字，在百度或者谷歌搜一搜即可，适合**高通**的救砖。SP Flash便是**联发科**对应的工具。

&emsp;如果进不了fastboot，或者想要更改基带信息等等更底层的信息。严谨来说是：EDL串口线刷模式。

**注意**：网上不一定能找到这些工具。