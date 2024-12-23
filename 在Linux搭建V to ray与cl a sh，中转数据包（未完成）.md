---
title: 在Linux搭建V to ray与cl a sh，中转数据包（未完成）
id: 33
date: 2024-12-03 17:09:49
auther: yutanbird
cover: https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/20221116083728.png
excerpt: 在Linux上使用clash，v2ray的基础教程
permalink: /archives/zhuan-shu-ju-bao
categories:
 - comb
tags: 
 - linux
---



# 注意

***下面所有用到的端口，都需要去你的Linux上开启防火墙***

# clash

## 下载并安装clash

&emsp;可以先到这里选择自己合适的版本 [clash github](https://github.com/Dreamacro/clash/releases)，我装的是云服务器，选择的是：-linux-amd64-v1.11.4.gz，也有在树莓派或者其他软件上面的，注意，老一点的版本应该是有arm架构的Linux的，所以可以往下翻一翻。

```shell
cd ~ # 我下面步骤都是在根目录操作的，当然，在哪操作都没关系，如果涉及到权限问题，自行加sudo
wget https://github.com/Dreamacro/clash/releases/download/v1.11.12/clash-linux-amd64-v1.11.12.gz
gzip -d ./clash-linux-amd64-v1.11.12.gz # 解压，也可以在电脑上解压好传过去
mv clash-linux-amd64-v1.11.12 clash #重命名一下，为了之后方便找
# 这一步可以选择在电脑上下好，然后用ftp等工具传到Linux上

cd ~/clash # 切换到clash文件下
ls # 会有一个clash文件
chmod +x ./clash # 加上可执行权
./clash # 运行测试一下

# 如果成功启动就 ctrl + c关闭

```

&emsp;上面把clash安装好了，下面开始搞配置文件，如果上面成功启动了，那么：

- ~/.config 下应该有一个clash文件夹

- ~/.config/clash 下面应该最起码有两个文件：

  > 1、Country.mmdb：这个防火墙文件，哪些需要过防火墙，哪些不需要
  >
  > 2、config.yaml：这个是你的配置，也就是订阅文件，每个人都不同

&emsp;假如没有 Country.mmdb 文件

```shell
wget https://github.com/Dreamacro/maxmind-geoip/releases/latest/download/Country.mmdb
# 没有的话，可以有上述命令下载，或者用ftp等工具传输过去也可
# 注意，这些文件都应该在 ~/.config/clash 下
```

## 配置

&emsp;默认生成的config.yaml是没有用的，我们需要用我们自己的机场文件覆盖掉，假设你 **已经** 用你自己的文件覆盖了这个文件。

我是从Windows上ftp过去的，Windows上的配置文件在：`C:\Users\xxxx\.config\clash\profiles`下，名字不一定是config.yaml文件，复制到Linux上的话，**一定** 得是config.yaml文件。

```yaml
# 我截取了配置文件中基本相同的
port: 7890  # http/https端口，自行设置，或者就用默认的即可
socks-port: 7891 # socks端口，同上
allow-lan: true # 允许局域网，设置为true
mode: Rule # 规则代理，默认即可
log-level: info # 默认即可
external-controller: :9090 # 这个是为了远程访问你的clash，叫做 api端口吧，自行设置，默认即可
secret: 'xxxxxxx' # 远程访问clash的密码
ipv6: true # 可能没有这句话，看你的机场适不适合用ipv6，反正设置为true没关系的

```

- 这一步没什么关系，但是可以像在Windows上访问你的clash，如更换节点，查看log等等， 可以不操作

  > 运行clash文件，像前面一样
  >
  > Windows上点击[clash](http://yacd.haishan.me/)，填入信息
  >
  > ![clash网页api操作](https://imagere.oss-cn-beijing.aliyuncs.com/img20220904/20221114190354.png)

- 之后就可以可视化操作了

***记得定期更新你的订阅，或者可以设置自动更新，比如Linux的定期执行命令的功能来自动更新***

## 服务启动

&emsp;有很多保证Linux软件后台运行的方法，我用的是服务启动

在 `/usr/lib/systemd/system/`下新建 `clash.service`文件

`clash.service`服务配置文件如下：

```yaml
[Unit]
Description=Clash Daemon
After=network.target

[Service]
# YOURCLASH：你的clash可执行文件的绝对路径，也就是前面的~/clash/clash
# YOURCLASHCONFIG：你的clash配置文件，也就是前面的~/.config/clas
# 一定要用绝对路径
ExecStart=YOURCLASH -d YOURCLASHCONFIG
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

&emsp;保存之后

```shell
systemctl daemon-reload # 更新你的服务文件，必须执行
systemctl start clash.service # 启动你的clash服务
systemctl status clash.service # 如果出现绿色的active就是成功
systemctl stop clash.service # 停止你的clash服务
systemctl enable clash.service # 设置开机自启动
systemctl disable clash.service # 关闭开机自启动
```



## 环境变量

&emsp;根据上述配置，并且运行或者启动了你的clash之后，使用

```shell
curl https://google.com

# 成功的话就是打印如下信息
# 当然，只要有非404响应就算成功
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TILE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
```

不一定能够成功，还需要配置环境变量

```shell
# 设置临时环境变量，关机就没了的，端口号根据自己的来
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7891

# 再次测试
curl https://google.com # 应该能够成功了

# 取消环境变量的设置，不需要用clash可以取消
unset http_proxy https_proxy all_proxy


# 上面是临时设置，下面这个是永久设置
vim ~/.bashrc # 打开这个文件

# 在最后一行加上
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7891
```

# v2ray

## 安装

``` shell
bash <(curl -L -s https://install.direct/go.sh)

chmod +x go.sh

./go.sh
```

## 配置

```shell
v2ray

# 然后配置你的v2ray或者ssr
# 下面是v2ray的输出
# 跟着操作就行


........... V2Ray 管理脚本 v3.45 by 233v2.com ..........

## V2Ray 版本: v4.44.0  /  V2Ray 状态: 正在运行 ##

帮助说明: https://233v2.com/post/1/

反馈问题: https://github.com/233boy/v2ray/issues

TG 频道: https://t.me/tg2333

捐赠脚本作者: https://233v2.com/donate/

捐助 V2Ray: https://www.v2ray.com/chapter_00/02_donate.html

  1. 查看 V2Ray 配置

  2. 修改 V2Ray 配置

  3. 下载 V2Ray 配置 / 生成配置信息链接 / 生成二维码链接

  4. 查看 Shadowsocks 配置 / 生成二维码链接

  5. 修改 Shadowsocks 配置

  6. 查看 MTProto 配置 / 修改 MTProto 配置

  7. 查看 Socks5 配置 / 修改 Socks5 配置

  8. 启动 / 停止 / 重启 / 查看日志

  9. 更新 V2Ray / 更新 V2Ray 管理脚本

 10. 卸载 V2Ray

 11. 其他

温馨提示...如果你不想执行选项...按 Ctrl + C 即可退出
```



# 中继代理

## 修改v2ray的配置文件

参考[官方教程](https://www.v2ray.com/chapter_02/01_overview.html)

```shell
cd /etc/v2ray # 配置文件在这

vim /etc/v2ray/config.json # 修改配置文件

# 主要是两个
# 1、Inbound 入站连接，也就是别人怎么连你的Linux
# 在前面用 v2ray 命令也可以配置

# 2、Outbound 出战连接，需要设置成本地的clash代理，127.0.0.1:7890
# 本地转发可以不用加密
# 我的Outboun配置如下

```

```json
...
"outbonds": [
    {
        "protocol": "freedom",
        "settings": {
         	"domainStratege": "AsIs",
            "address": "127.0.0.1:7890",
            "userLevel": 0
        },
        "tag": "direct"
    }
]
...
```

## 下载v2ray配置链接到其他客户端

```shell
v2ray

# 然后配置你的v2ray或者ssr
# 下面是v2ray的输出
# 跟着操作就行


........... V2Ray 管理脚本 v3.45 by 233v2.com ..........

## V2Ray 版本: v4.44.0  /  V2Ray 状态: 正在运行 ##

帮助说明: https://233v2.com/post/1/

反馈问题: https://github.com/233boy/v2ray/issues

TG 频道: https://t.me/tg2333

捐赠脚本作者: https://233v2.com/donate/

捐助 V2Ray: https://www.v2ray.com/chapter_00/02_donate.html

  1. 查看 V2Ray 配置

  2. 修改 V2Ray 配置

  3. 下载 V2Ray 配置 / 生成配置信息链接 / 生成二维码链接

  4. 查看 Shadowsocks 配置 / 生成二维码链接

  5. 修改 Shadowsocks 配置

  6. 查看 MTProto 配置 / 修改 MTProto 配置

  7. 查看 Socks5 配置 / 修改 Socks5 配置

  8. 启动 / 停止 / 重启 / 查看日志

  9. 更新 V2Ray / 更新 V2Ray 管理脚本

 10. 卸载 V2Ray

 11. 其他

温馨提示...如果你不想执行选项...按 Ctrl + C 即可退出

# 上述的 3 就是生成v2ray链接，7 就是生成ssr链接
```

## 附加一份完整v to ray的json配置

```json
{
	"log": {
		"access": "/usr/local/etc/v2ray/log/access.log",
		"error": "/usr/local/etc/v2ray/log/err.log",
		"loglevel": "info"
	},

	"inbounds": [
		{
			"port": 57***, 
			"protocol": "vmess",
			"settings": {
				"clients": [
					{
						"id": "b1******-****-****-****-**********11"
					}
				]
			},
			"tag": "fromuser"
		}
	],
	"outbounds": [
		{
			"sendThrough": "0.0.0.0",
			"protocol": "socks",
			"settings": {
				"servers": [
					{
						"address": "127.0.0.1",
						"port": 7891, // c l a s h的socket5代理设置为7891
						"users": []
					}
				]
			},
			"tag": "toclash",
			"streamSettings": {},
			
			"mux": {}
		}
	],
	"routing": {  // 只设置了一个默认的路由
		"domainStrategy": "AsIs",
		"rules": [
			{
				"type": "field",
				"domain": [
					".*"
				],
				"network": "tcp,udp",
				"inboundTag": [
					"fromuser"
				],
				"outboundTag": "toclash"
			}
		],
		"balancers": []
	}
}
```

