---
title: 树莓派上使用openwrt实现单线多播
date: 2023-10-20 14:07:23.0
updated: 2023-10-22 10:54:20.662
url: /archives/raspberryopenwrtmultidial
categories: 
- 编译与配置
tags: 
- openwrt
---



# 前言

&emsp;如何安装openwrt请看另一篇[博客](https://lmzyoyo.top/archives/installopenwrt-open-cla-sh)

**注意**：一定要检查你的单线是不是带宽够，如果单线带宽只有百兆，就别白费劲了。

# MACVLAN

&emsp;默认情况下，单个接口具有一个 MAC，可以通过 DHCP 获取一个 IPv4 地址。如果需要多拨，则需要多个网络接口，此时仅仅一个 WAN 是不够的。一种简单的想法是将 br-lan 网桥中用做 LAN 的接口拿出，配置为新的 WAN 口，从而拿到更多 IP 地址。问题在于这需要从上级交换机连接多根网线，非常不便。何况路由器的网卡是有限的，可能不够使用。

&emsp;利用 MacVLAN 技术，可以很方便的解决这个问题。关于 MacVLAN 的详细介绍可以参考[这篇文章](https://icloudnative.io/posts/netwnetwork-virtualization-macvlan/)。简单地讲，MacVLAN 技术可以在单个物理网卡上虚拟出多个 MAC 地址不同的虚拟网卡。当物理网卡收到数据包后，会根据目的 MAC 地址判断这个包属于哪一个虚拟网卡。因此，创建多个 MacVLAN 虚拟网卡，即可从 DHCP 服务器获取多个 IP 地址。具体的配置方法如下：[<sup>1</sup>](#yy1)



- 添加macvlan设备：点击网络-接口-设备-创建，需要创建若干个啊，然后注意MAC地址需要有变化，建议更改最后一位即可。注意我第二张图有MAC地址啊，可以参考一下。然后点击保存并应用

    ![image-20231002013619152](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310020136210.png)

    ![image-20231002015403161](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310020154217.png)

- 创建若干个接口：点击网络-接口-创建，数量和上面一样，每个接口分别选择一个MACVLAN。并且每个接口都需要设置跃点数，分别为10 20 30，不能一样啊

    ![image-20231002014306522](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310020143593.png)

    ![image-20231002014239431](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310020142493.png)

- 防火墙可以选择WAN策略，不是必须啊

    ![image-20231002013100866](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310020131929.png)

- 最终连上网络看看，正常的话，你已经有ip地址了的

    ![image-20231002121655899](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310021216978.png)

- 上面图是自动跳出来的登录窗口，但是显然只有SEU1在工作，因此，我们需要对每个接口都进行登录，停止所有登录好了的接口，再重新登录。

    或者，可以在下面配置中策略单独加几个策略，并且只使用单个MACVLAN，然后在规则中导入这些策略，目的地址是你的登录地址的，并且把这些规则放在最前面。

# 配置均衡

- 点击网络-MultiWAN管理器-接口。删除所有接口，输入刚刚在前面添加的接口名称，我这里是SEUx。1.弹窗勾选启用；2.跟踪的主机或ip地址，填一个百度的ip即可，我填的自己服务器的ip。

    ![image-20231010220048488](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310102200552.png)

- 点击成员，删除所有，下方添加webx，总共四个。跃点数和weight都设置为1

    ![image-20231002121429108](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310021214160.png)

- 点击策略，只保留balanced策略，其他删除，成员设置成webx四个

    ![image-20231002121501561](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310021215601.png)

- 点击规则，保留default_rule_v4和default_rule_v6，其他删除。v4设置成balanced，v6设置成default（默认）。

    ![image-20231002121614178](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310021216242.png)

# 校园网登录

&emsp;采用手动登录的方法，其实可以用脚本自动登录的，multiWAN的通知是可以执行脚本的，这里介绍手动登录的方法。

直接给MultiWAN配置图吧，无非是多加了单网卡的配置

![](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310102200552.png)

![image-20231010220116221](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310102201272.png)

![image-20231010220129506](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310102201563.png)

![image-20231010220156625](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310102201675.png)

记得，这个规则这里，有一行`login_net`，编辑它，依次分配的策略，依次登录（注意，我登录的地址是`10.9.10.100`，`/32`需要加上）。请根据自己的来。



登录完成，请看`状态-MultiWAN管理器`

![image-20231010220625653](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310102206699.png)

# 删除默认网桥，改成AP

## 新建好AP

网络->无线，中把WLAN开启

## 更改ssh登录

系统->管理权->ssh访问，中更改成你的WLAN登录

# 一些调试命令

```shell
route # 查看默认路由表

nload eth0 -m # 查看以太网口实时网速


```



# speedtest



# 附言

其实整个测试，多拨都没有效果，最后发现电脑网线裸连速度更快

![image-20231011232236290](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310112322388.png)

![image-20231011232242444](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310112322481.png)

![image-20231011232247470](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310112322540.png)

# 参考链接

<div id="yy1"></div>

- [Myth的博客](https://myth.cx/p/openwrt-macvlan-mwan3/)