---
title: 有关docsify的markdown速成
id: 85
date: 2024-12-03 17:09:55
auther: yutanbird
cover: 
excerpt: 初始环境下载与新建点击下载typora
permalink: /archives/docsifywithmarkdown
categories:
 - default
tags: 
---


# 网站地址

网站地址：[https://english.lmzyoyo.top](https://english.lmzyoyo.top)，可随时进网站查看，当然，现在只有框架。

# 初始环境

## 下载与新建

[点击下载typora](https://files.lmzyoyo.top/s/VjTD)，密码是：lmzmarkdown

- 请在你任何地方新建一个文件夹，假设名字是：0x_English，x是你的序号
- 然后 **进入** 该文件夹再新建一个文件夹，名字请以 0x_img 命名，x是你的序号
- 然后继续在 0x_English 目录下新建一个文件，名字随便，我以 01_introd 命名的。**但是**，扩展名必须是 `md`
- 文件目录如下所示

```
- 0x_English
	|
	|- 0x_img
	|- 0x_filename.md
```

![image-20231008205456096](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082054130.png)

## 开始编写

请使用刚刚下载的`typora`软件打开`md`文件，就可以开始写的，记得经常性地手动保存你的文件哦！`ctrl+S`即可。

# 如何写标题

```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题

所以，其实用法就是，一个“#”加上一个空格，然后写上标题的文字，简单吧！！！
```

**ps**：左边会自动生成目录，但是仅仅生成一、二级标题，三、四级标题不会生成

![image-20231008204707754](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082047845.png)

# 如何加图片

&emsp;请把图片放到刚刚的`0x_img`目录下，并且请规范一下命名，以`0x_`开头来命名你的图片文件，（x是你的序号），比如下面这样。

![image-20231008205910498](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082059529.png)

现在的目录结构就变成了如下这样

```
- 0x_English
	|
	|- 0x_img
		|
		|- 01_test.png
	|- 0x_filename.md
```

回到你的md文件，请按照下面的格式添加文件即可

```
![图片描述](图片路径)

比如：
![图1 测试图片](./01_img/01_test.png)

注意，这里图片路径的写法
```

# 如何添加链接

```
[链接的描述](你的url链接)

比如：
[百度百科](https://baidu.com)
```

![image-20231008210735861](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082107888.png)

# 如何处理文本

- 加粗：左右用 **两** 个 `*` 把需要加粗的文字包起来
- 加斜：左右用 **一** 个 `*` 把需要加斜的文字包起来
- 其他同理啊，看下面就知道了

```
**加粗**

*斜线*

***加粗加斜线***

<u>下划线</u>

~~删除线~~

```

![image-20231008210416175](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082104215.png)

# 如何分点

**值得注意**的是，你在typora中，写完一行分点后敲一下回车他会自动给你打上下一行的，你直接写就行，跟word很像。注意空格啊

```
1. 这是有序分点1
	1. 这是有序分点1.1
	2. 这是有序分点1.2
2. 这是有序分点2


> 这是一段文字
> >
> > 这是一段文字2
> > 
> > 这是一段文字3
> > 
> 这是一段文字4

- 这也是无序分点
- 这也是无序分点
```

![image-20231008211301644](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082113695.png)

我更加推荐的是：

```
> 这样的一行写法
```

# 如何增加提示或者警告

**注意：**这个只有在网站上才会渲染，在typora中就会是最下面那样

```
> [!TIP] 
> 这是一行提示

> [!Danger] 
> 这是一行危险

> [!WARNING] 
> 这是一行警告

> [!NOTE] 
> 这是一行信息
```

![image-20231008211454008](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082114060.png)

在typora中就是这样的，没有任何表示，继续写就是了

![image-20231008211605915](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082116957.png)

# 普通文字呢？

普通文字正常写就行，需要注意的是，你的`tab`没法直接tab，需要用 `&emsp;`来替代

```
&emsp;这是一大段的开始，前面其实会有一个tab的距离

这是没有tab的测试
```

![image-20231008212025149](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082120183.png)

# 转义字符

现在我们前面发现了很多字符好像被用来加粗加斜等了，那么怎么打出他们来呢？比如星号怎么打出，和C语言一样，加一个反斜杠`\`即可如下面

```
\*

\\

\< 

\> 
```

![image-20231008212211248](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082122279.png)

# 如何增加参考文献呢？

```
&emsp;我将引用一段维基百科的内容：[^wiki]

在你的文章最后面写下，其实冒号后面就是链接的写法，注意有空格:

[^wiki]: [维基百科内容](http://wiki.org)
```

![image-20231008212558261](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082125297.png)

注意，最后的一行其实会消失的，但是在typora中渲染确实这样的，和网站有所不同，不用管这么多，继续写就好

![image-20231008212658644](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310082126673.png)

