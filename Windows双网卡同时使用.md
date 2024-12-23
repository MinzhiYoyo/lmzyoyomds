---
title: Windows双网卡同时使用
id: 84
date: 2024-12-03 17:09:55
auther: yutanbird
cover: 
excerpt: 受限于学校网络，坑死了
permalink: /archives/doubleinterface
categories:
 - default
tags: 
 - 双网卡
---


# 前言

&emsp;由于受限于单网卡带宽，因此尝试了双网卡的方式进行下载。

# 配置

## 硬件要求

连接wifi和有线

## 查看跃点数

打开cmd窗口，输入`route print`，发现有线网和wif不是相同的跃点数，记住两者的最小值，我的是25。

![image-20230916122500202](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161225227.png)



## 更改跃点数

首先打开`控制面板` - `网络和Internet` - `网络和共享中心` ，点击左边的更改适配器设置 。

![image-20230916122136999](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161221111.png)

- 1.更改WLAN

双击WLAN-> `属性` -> `Internet协议版本4` -> `高级` -> `接口跃点数`

把接口跃点数改成刚刚查看的跃点数最小值，改成刚刚记住的最小值。

![image-20230916123210570](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161232655.png)

- 2.更改以太网的接口跃点数。同样地

双击以太网-> `属性` -> `Internet协议版本4` -> `高级` -> `接口跃点数`

把接口跃点数改成刚刚查看的跃点数最小值，改成刚刚记住的最小值。

# 查看效果

## 单网卡下载

![单独网卡下载](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161237916.png)

![单独网卡下载2](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161237671.png)



## 双网卡下载

![双网卡下载](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161237469.png)

![双网卡下载2](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161237967.png)

![双网卡下载3](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202309161238065.png)

# 结束

可以看出，双网卡的话，是两个网卡（以太网和wif）同时下载。单网卡是一个网卡（以太网）下载。

***注意***：如果下载本身不支持多线程或者切片下载，那么也不太能使用这种方式的。至于能不能增快网速，这就没测试过了。因为我们学校只是限制了带宽而已。