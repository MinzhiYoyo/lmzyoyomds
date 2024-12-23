---
title: Windows右键打开注册表配置
id: 11
date: 2024-12-03 17:09:45
auther: yutanbird
cover: http://imagere.oss-cn-beijing.aliyuncs.com/img/20220605141605353731.png
excerpt: 为你的右键添加一个项目
permalink: /archives/windows%E5%8F%B3%E9%94%AE%E6%89%93%E5%BC%80%E6%B3%A8%E5%86%8C%E8%A1%A8%E9%85%8D%E7%BD%AE
categories:
 - default
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