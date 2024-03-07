---
title: 为Halo—Dream设置自己的live2d静态资源
date: 2022-11-28 18:42:22.0
updated: 2023-01-02 07:34:27.809
url: /archives/wei-halodream-she-zhi-zi-ji-de-live2d-jing-tai-zi-yuan
categories: 
- 爬虫与前端
tags: 
- 博客
---

# 设置域

&emsp;为你的域名另外添加一个域，用于live2d静态资源的获取。演示阿里云域名添加域。

![image-20221128183840843](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128183840843.png)

# 添加站点

![image-20221128183958129](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128183958129.png)

# 安装

&emsp;首先，到[git仓库](https://github.com/nineya/live2d-widget-model)把[玖涯（dream作者）](https://blog.nineya.com/)的静态资源clone到你的服务器，路径为`/www/wwwroot/live2d.xxxx.com`(你的站点路径，也就是上一步你的 **根目录** )

```shell
cd /www/wwwroot/live2d.xxxx.com # 切换到站点目录
git clone https://github.com/nineya/live2d-widget-model.git live2dmodel # clone一下仓库，并且我命名为了live2dmodel
```

# 配置跨域问题

&emsp;打开你的宝塔

![image-20221128184541007](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128184541007.png)

```conf
    add_header Access-Control-Allow-Origin https://lmzyoyo.top; # 只允许我的域访问，也就是我的网站域，记住设置成自己的域
    add_header Access-Control-Allow-Headers Origin,X-Requested-With,Content-Type,Accept; 
    add_header Access-Control-Allow-Methods GET; # 添加GET就行，静态资源用不到POST等
```

# 配置SSL安全访问

![image-20221128184847837](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128184847837.png)

# 粘贴到主题中

你的域应该是：`https://live2d.xxxx.com/live2dmodel`

域名和文件夹名对应好就行

![image-20221128191608490](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128191608490.png)



# 查看效果

***记得清除一下浏览记录***

然后重新打开[自己的博客](https://lmzyoyo.top)查看效果即可

![image-20221128185328505](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128185328505.png)

并且也没法出现跨域问题报错

![image-20221128185416030](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/image-20221128185416030.png)

