---
title: Windows右键打开注册表配置
date: 2022-04-05 19:27:00.0
updated: 2023-01-02 07:33:56.897
url: /archives/windows右键打开注册表配置
categories: 
- 默认分类
tags: 
---


win+R regedit调出注册表

```shell
计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\*\shell\
```

> 上面这个路径下新建项：Open with ……

> 然后Open with ……新建项Command

> Command 默认为 路径\…….。exe  "%1"

> 然后Open with ……新建Icon字符串值

> 为路径\…….。exe

![image-20210102212700378](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141606230245.png)

![image-20210102212729670](http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141606775108.png)