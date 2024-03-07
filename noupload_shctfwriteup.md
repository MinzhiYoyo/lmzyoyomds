---
title: SHCTFwriteup
date: 2023-10-03 19:27:00.0
updated: 2023-10-03 07:33:56.897
url: /archives/shctfwriteup
categories: 
- CTF
tags: 
- writeup
- shctf
---

# 0x00 真的签到题

![image-20231003200205673](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032002788.png)

`flag{Welc0me_tO_SHCTF2023}`

# 0x01 [WEEK1] 残缺的md5

## 题目描述：

```
苑晴在路边捡到了一张纸条，上面有一串字符串：KCLWG?K8M9O3?DE?84S9
问号是被污染的部分，纸条的背面写着被污染的地方为大写字母，还给了这串字符串的md5码值：F0AF????B1F463????F7AE???B2AC4E6
请提交完整的md5码值并用flag{}包裹提交
```

## 题目解析：

我们只需要遍历这三个问号即可，使用python来帮助我们解决

```python
import hashlib
def strtomd5(s:str):
    return hashlib.md5(s.encode(encoding='utf8')).hexdigest().upper()

# s1带问号
def equal(s2:str):
    sd = "F0AF????B1F463????F7AE???B2AC4E6"

    if not len(sd) == len(s2):
        return False

    for i in range(len(sd)):
        if not sd[i] == s2[i] and not sd[i] == '?':
            return False
    return True

# sr[5] sr[12] sr[15]
sr = list("KCLWG?K8M9O3?DE?84S9")
def next(s: str = None):
    if s and not len(s) == 1:
        return
    if not s:
        return 'A'
    o = ord(s[0])
    if (o < ord('Z') and o >= ord('A')):
        return chr(o+1)
first = next()
seconde = next()
third = next()
Flag = False
result = []
while first and not Flag:
    # print("first = {}".format(first))
    sr[5] = first
    while seconde and not Flag:
        # print("seconde = {}".format(seconde))
        sr[12] = seconde
        while third and not Flag:
            # print("third = {}".format(third))
            sr[15] = third
            md = strtomd5("".join(sr))
            # print(md)
            if equal(md):
                s = "成功！原始：'{}', md5: '{}'".format("".join(sr), md)
                result.append(s)
                print(s)
                Flag = True
            third = next(third)
        third = next()
        seconde = next(seconde)
    seconde = next()
    first = next(first)
if not Flag:
    print("失败")
```

# 0x02 [WEEK1]凯撒大帝

## 题目描述

```
pvkq{mredsrkyxkx}
```

## 题目解析

直接在在线网站上尝试即可

![image-20231003200823086](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032008160.png)

# 0x03 [WEEK1]熊斐特

## 题目描述

```
熊斐特博士发现了一种新的密码。
uozt{zgyzhs xrksvi}
```



## 题目解析

网上搜索资料可得，熊斐特解密就是字母表首尾对应，也就是 a-z b-y，依次下去，如下

```
a b c d e f g h i j k l m n o p q r s t u v w x y z
z y x w v u t s r q p o n m l k j i h g f e d c b a
```

因此，`uozt{zgyzhs xrksvi}`就会对应成`flag{atbash cipher}`

# 0x04 [WEEK1]迷雾重重

## 题目描述

```
题目描述：

morse？ASCII？


密文：

0010 0100 01 110 1111011 11 111 010 000 0 001101 00 000 001101 0001 0 010 1011 001101 0010 001 10 1111101
```



## 题目解析

这是显然提示我们使用 ASCII表和摩斯密码表来解决的，问题就在如何区分是使用哪个表，显然只有 `{}`这两个符号使用ASCII表

答案：

```
0010 0100 01 110 1111011 11 111 010 000 0 001101 00 000 001101 0001 0 010 1011 001101 0010 001 10 1111101
F L A G { M O R S E _ I S _ V E R Y _ F U N }
```

# 0x05 [WEEK1]飞机大战

## 题目描述

题目会给我们一个飞机大战的游戏

## 题目解析

首先，打开网站，使用浏览器抓包

输了以后，会给出下图提示，因此，我们可以去看看JS代码

![image-20231003201459387](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032014418.png)



打开js代码，注意到跟99999有关的地方有赢的函数：

![image-20231003201643584](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032016621.png)

跳转到这个函数

![image-20231003201720718](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032017748.png)

这个flag都不需要后台返回，因此简单的把这段代码用浏览器控制台跑一遍即可

![image-20231003201824756](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032018785.png)

![image-20231003201832332](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032018355.png)

# 0x06 [WEEK1]生成你的邀请函吧~

## 题目描述

```
API：url/generate_invitation  
Request：POST application/json  
Body：{  
    "name": "Yourname",  
    "imgurl": "http://q.qlogo.cn/headimg_dl?dst_uin=QQnumb&spec=640&img_type=jpg"  
}  

使用POST json请求来生成你的邀请函吧~flag就在里面哦~
```

## 题目解析

显然题目已经告诉我们使用 post json请求即可，使用python的requests库来完成

```python
import requests as rq

url = 'http://112.6.51.212:32824/generate_invitation'
r = rq.post(url=url, json={
    'name': 'yutanbird',
    'imgurl': 'http://q.qlogo.cn/headimg_dl?dst_uin=QQnumb&spec=640&img_type=jpg'
})

with open("邀请函.jpg", 'wb') as f:
    f.write(r.content)

```

然后我们打开图片即可看到flag

![image-20231003202125925](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032021995.png)

# 0x07 [WEEK1]signin

## 题目描述

题目给了我们一个程序 `signin.exe`

## 题目解析

我们在控制台运行只会打印hello world

使用IDA进行反编译

main函数里面直接写出来了

![image-20231003202345480](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202310032023522.png)

`flag{flag1sinarray}`

# 0x08 [WEEK1]nc

## 题目描述

无

## 题目解析

直接nc就行，在Linux中输入

```shell
nc ip port  # 即可，输入实例的ip地址和端口
```

# 0x09 [WEEK1]hard nc

## 题目描述

无

## 题目解析

先nc连接

```shell
nc ip port # 输入实例的ip地址和端口
```

查看目录下的文件

```shell
ls -lah

total 72K
drwxr-x---  1 0 1000 4.0K Oct  5 15:19 .
drwxr-x---  1 0 1000 4.0K Oct  5 15:19 ..
-rwxr-x---  1 0 1000  220 Apr  4  2018 .bash_logout
-rwxr-x---  1 0 1000 3.7K Apr  4  2018 .bashrc
-r--r--r--  1 0    0   81 Oct  5 15:19 .gift
-rwxr-x---  1 0 1000  807 Apr  4  2018 .profile
drwxr-x---  2 0 1000 4.0K Sep 26 11:43 bin
drwxr-xr-x  2 0    0 4.0K Sep 26 11:43 dev
-r--r--r--  1 0    0   32 Oct  5 15:19 flag
drwxr-xr-x  2 0    0 4.0K Oct  5 15:19 gift2
drwxr-x--- 15 0 1000 4.0K Sep 26 11:43 lib
drwxr-x---  3 0 1000 4.0K Sep 26 11:43 lib32
drwxr-x---  2 0 1000 4.0K Sep 26 11:43 lib64
-rwxr-x---  1 0 1000 8.4K Sep 26 11:42 pwn

```

发现特别的有 .gift flag gift2 pwn

`.gift`是一个文件，先查看

```shell
cat .gift

flag{30b78682-ae7e-4e
just a part of flag
try to find another flag
Come on, guys
```

再次发现`gift2`文件夹，cd进去看看

```shell
ls -lah
total 16K
drwxr-xr-x 2 0    0 4.0K Oct  5 15:19 .
drwxr-x--- 1 0 1000 4.0K Oct  5 15:19 ..
-r--r--r-- 1 0    0  123 Oct  5 15:19 flag2
```

发现这里有个flag2，查看一下

```shell
cat flag2
OTYtODBhZC0yNjBjMmZkZjNiZDN9Cg==
congratulations you find another part of flag
but you need to decode it
try to recall it.
```

`OTYtODBhZC0yNjBjMmZkZjNiZDN9Cg==`显然是base64编码，解码一下是：`96-80ad-260c2fdf3bd3}`

两个拼接即可：`flag{30b78682-ae7e-4e96-80ad-260c2fdf3bd3}`

# 0x0A 