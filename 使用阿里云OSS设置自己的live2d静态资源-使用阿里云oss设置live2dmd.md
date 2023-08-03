---
title: 使用阿里云OSS设置自己的live2d静态资源
date: 2022-11-28 19:25:23.638
updated: 2023-01-02 07:34:22.823
url: /archives/使用阿里云oss设置live2dmd
categories: 
- 爬虫与前端
tags: 
- 前端
---

# 下载并解压

&emsp;首先，到[git仓库](https://github.com/nineya/live2d-widget-model)把[玖涯（dream作者）](https://blog.nineya.com/)的静态资源下载到计算机上，然后解压并上传到OSS上。

![image-20221128191313721](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128191313721.png)

# 获取live2d链

![image-20221128191513805](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128191513805.png)

粘贴到主题中

![image-20221128191608490](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128191608490.png)

# 配置跨域

![image-20221128192313681](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128192313681.png)

![image-20221128192435275](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128192435275.png)

# 查看效果

***记得清除一下浏览记录***

然后重新打开[自己的博客](https://lmzyoyo.top)查看效果即可

![image-20221128185328505](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128185328505.png)

并且也没法出现跨域问题报错

![image-20221128185416030](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128185416030.png)