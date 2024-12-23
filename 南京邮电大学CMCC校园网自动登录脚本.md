---
title: 南京邮电大学CMCC校园网自动登录脚本
id: 68
date: 2024-12-03 17:09:52
auther: yutanbird
cover: 
excerpt: 自动校园网登录脚本，使用bat实现，无需添加任何环境。但是仅限于Windows端。
permalink: /archives/njuptcmccscriptbat
categories:
 - spider
tags: 
 - 爬虫
---

# 新建脚本文件
&emsp;假如你在D盘新建了一个文件名为`cmcc.bat`，其路径为：`D:\cmcc.bat`
如下图所示：
![](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202304132317073.png?x-oss-process=style/yasuo)

然后用记事本打开这个文件，复制下列代码
```shell
@echo off
netsh wlan connect name=NJUPT-CMCC > nul

echo will connect to %1 of NJUPT_CMCC, please wait 4 seconds
timeout /T 4 /NOBREAK

for /f "tokens=16" %%i in ('ipconfig ^|find /i "ipv4"') do (
	set myip=%%i
	goto out
)
:out
echo my wlan ip is %myip%


echo test connect
ping baidu.com -n 1 > nul
IF ERRORLEVEL 1 (

	echo disconnect, will connect %1 to NJUPT-CMCC

	ping p.njupt.edu.cn -n 1 > nul
	IF not ERRORLEVEL 1 (
		curl http://p.njupt.edu.cn:801/eportal/?c=ACSetting^&a=Login^&protocol=http:^&hostname=p.njupt.edu.cn^&iTermType=1^&wlanuserip=10.163.168.60^&wlanacip=null^&wlanacname=XL-BRAS-SR8806-X^&mac=00-00-00-00-00-00^&ip=10.163.168.60^&enAdvert=0^&queryACIP=0^&loginMethod=1 ^
		-X POST^
		-H "Accept-Encoding=gzip, deflate"^
		-H "Origin: http://p.njupt.edu.cn"^
		-H "Referer: http://p.njupt.edu.cn/"^
		-H "Host: p.njupt.edu.cn:801" ^
		-H "Connection: keep-alive" ^
		-A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36"^
		-e "http://p.njupt.edu.cn" ^
		--data-urlencode "DDDDD=,0,%1@cmcc" ^
		--data-urlencode "upass=%2" ^
		--data-urlencode "R1=0" ^
		--data-urlencode "R2=0" ^
		--data-urlencode "R3=0" ^
		--data-urlencode "R6=0" ^
		--data-urlencode "para=00" ^
		--data-urlencode "0MKKey=%2" ^
		--data-urlencode "buttonClicked=" ^
		--data-urlencode "redirect_url=" ^
		--data-urlencode "err_flag="^
		--data-urlencode "username="^
		--data-urlencode "password="^
		--data-urlencode "user="^
		--data-urlencode "cmd="^
		--data-urlencode "Login="^
		--data-urlencode "v6ip="
	) ELSE (
		echo cannot find p.njupt.edu.cn
	)		
	echo please wait 4 seconds
	timeout /T 4 /NOBREAK
	ping baidu.com -n 1 > nul
	IF not ERRORLEVEL 1 (
		echo connect success
	) ELSE (
		echo connect failed
	)
) ELSE (
	echo You have connected
)

pause

```

保存并退出

# 创建快捷方式
![image](https://imagere.oss-cn-beijing.aliyuncs.com/HaloFiles/image.png?x-oss-process=style/yasuo)

然后右键你创建的快捷方式，并点击属性
![image-1681399223187](https://imagere.oss-cn-beijing.aliyuncs.com/HaloFiles/image-1681399223187.png?x-oss-process=style/yasuo)

在目标这里添加账号和密码，中间用空格分隔
![image-1681399313413](https://imagere.oss-cn-beijing.aliyuncs.com/HaloFiles/image-1681399313413.png?x-oss-process=style/yasuo)

然后想把这个快捷方式放到哪都行，之后双击打开快捷方式，实现的功能有：自动连接`NJUPT-CMCC`，自动上传账号密码（忽视系统代理）

# 更改图标
还可以在属性中更改图标
