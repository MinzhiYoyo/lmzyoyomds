---
title: 广播地址
date: 2022-12-06 21:40:33.598
updated: 2023-04-04 17:41:57.605
url: /archives/guang-bo-de-zhi
categories: 
- 考研计算机网络
tags: 
- 广播地址
---

# 255.255.255.255
&emsp;这个又称之为本地广播地址，常用于没有获取IP地址时的广播地址，如DHCP发现报文中的目的地址。
# 192.168.1.255
&emsp;这个又称之为定向广播地址，首先你得需要一个IP地址，才能知道定项广播地址
# 路由器的工作
&emsp;路由器只会转发一种广播地址，也就是非该网络的广播地址，其他任意广播地址，路由器将不会转发到其他子网，这也就是为什么能够隔离广播风暴。

&emsp;而对于本子网中，路由器也只会转发定向广播地址，不会转发本地广播地址（也就是255.255.255.255）
