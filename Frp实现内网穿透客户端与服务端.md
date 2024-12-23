---
title: Frp实现内网穿透客户端与服务端
id: 55
date: 2024-12-03 17:09:53
auther: yutanbird
cover: 
excerpt: frp实现内网穿透
permalink: /archives/frpneiwang
categories:
 - comb
tags: 
 - 内网穿透
---



# 安装

[下载页面](https://github.com/fatedier/frp/releases)

客户端与服务端各自下载自己的`release`

# 配置服务启动

`frpServer.service`文件如下：

```shell
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /path/to/frps -c /path/to/frps.ini # 服务器
# ExecStart = /path/to/frpc -c /path/to/frpc.ini # 客户端
# 两者启动一个就行

[Install]
WantedBy = multi-user.target
```

# 配置文件

以下端口需要在云服务器的防火墙打开`tcp`，另外，域名也需要增加解析

服务端：`frps.ini`

``` ini
bind_port = 7000  # 连接端口，由客户端使用
vhost_http_port = 7001 # 绑定端口，之后就用这个端口访问
dashboard_user = 你的用户名
dashboard_pwd = 你的密码
token = 你的token
```

客户端：`frpc.ini`

```ini
[common]
server_addr = 服务器的域名
server_port = 7000 # 与上面的bind_port端口保持一致
dashboard_user = 你的用户名
dashboard_pwd = 你的密码
token = 你的token

[web]
type = http
local_port = 80
custom_domains = n1.youdomain.com

[web2]
type = http
local_port = 8080
custom_domains = n2.youdomain.com
```

# 尝试访问

在浏览器输入：`http://n1.youdomain.com:7001`，那么就访问客户端的`80`端口

在浏览器输入：`http://n2.youdomain.com:7001`，那么就访问客户端的`8080`端口

