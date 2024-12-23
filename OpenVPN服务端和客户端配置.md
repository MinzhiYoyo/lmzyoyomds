---
title: OpenVPN服务端和客户端配置
id: 161
date: 2024-12-03 17:09:59
auther: yutanbird
cover: 
excerpt: 用服务器搭建局域网来打游戏吧
permalink: /archives/openvpnserverandclient
categories:
 - 密码学
 - linux
tags: 
 - openvpn
---

# 密码学基础知识

对称加密就是，双方有同样的密钥，对一份数据进行加密解密。

非对称加密就是，甲方有公钥私钥，用私钥进行加密，别人就能用公钥进行解密。如果用公钥进行加密，只能用私钥进行解密。公钥是公开的，私钥只有甲方自己保留，绝不公开。可计算性理论上，目前非对称加密是绝对安全的。量子计算机出世之后，目前的非对称加密方案就变得不安全了。

哈希运算，对一份任意长度的数据进行哈希运算，生成一份固定长度的哈希值，这份哈希值是前面数据的摘要。如果数据变化了，那么哈希值一定会变。

# 数字证书

先介绍一下数字证书相关概念，数字证书可以证明公钥确实来自于服务器，假设Alice和Bob通信。

Alice向Bob索要其数字证书，只需要验证该数字证书是否有效，就可以验证Bob的身份是否正确了。数字证书如何来的呢？这里引入CA（Certificate Authority, 证书颁发机构）的概念，数字证书就是它颁发的。

-   Bob首先生成公私钥对，设定有效期、组织名、国家省份等等元数据。将元数据和公钥对打包成一份数字证书，并发送给CA1（注意，这个过程会验证CA1的证书）
-   CA1收到这个数字证书，用哈希算法（比如sha256）对(元数据+公钥)进行哈希（记为h1），并用私钥对h1进行加密，得到hc1。拼接好(元数据+公钥+hc1)组成完整的数字证书，发送给Bob。
-   任何人（比如Alice）向Bob通信会先索要数字证书，并进行验证数字证书的公钥信息及其他信息，最起码会验证公钥信息。
-   验证过程：先对（元数据+公钥）进行同样的哈希运算（sha256）得到ha1，然后用CA1的公钥进行解密得到had1，比较ha1和had1是否相同，相同就说明数字证书正确，也就是公钥正确。
-   那么有个问题，CA1的数字证书（或者公钥）如何验证？有更高级的CA来验证CA1的数字证书，更更高级的CA来验证更高级的CA的数字证书，…，有个称为根CA的机构，来验证最终的数字证书。根CA的机构的数字证书往往会预装在操作系统中的，也可以手动安装。

# TUN and TAP

在[计算机网络](https://zh.wikipedia.org/wiki/计算机网络)中，**TUN**与**TAP**是操作系统内核中的虚拟网络设备。不同于普通靠硬件[网络适配器](https://zh.wikipedia.org/wiki/网络适配器)实现的设备，这些虚拟的网络设备全部用软件实现，并向运行于[操作系统](https://zh.wikipedia.org/wiki/操作系统)上的软件提供与硬件的网络设备完全相同的功能。**TAP**等同于一个[以太网](https://zh.wikipedia.org/wiki/以太网)设备，它操作[第二层](https://zh.wikipedia.org/wiki/OSI模型)数据包如[以太网](https://zh.wikipedia.org/wiki/以太网)数据帧。**TUN**模拟了[网络层](https://zh.wikipedia.org/wiki/网络层)设备，操作[第三层](https://zh.wikipedia.org/wiki/OSI模型)数据包比如[IP](https://zh.wikipedia.org/wiki/IP地址)数据包。[^1]



# 安装

使用`easy-rsa`生成证书，因此也需要安装这个软件，在centos上操作，安装真的非常简单

```shell
yum install easy-rsa openvpn  # 客户端不用easy-rsa
apt install easy-rsa openvpn # 客户端不用easy-rsa

# windows打开 https://openvpn.net/community-downloads/
```

# 配置

## 准备工作

```shell
# 我们直接执行easyrsa是无用的，会提示下列功能
which easyrsa  # 或者which easy-rsa
/usr/bin/which: no easyrsa in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin)

# 我们确实安装成功了easy-rsa，因为它在下面的目录
# /usr/share/easy-rsa/3/

# openvpn的配置文件默认放在 /etc/openvpn/server /etc/openvpn/client
# 不妨在/etc/openvpn下新建easy-rsa文件夹

cd /etc/openvpn && mkdir easy-rsa
cd easy-rsa
cp -r /usr/share/easy-rsa/3/* .  # 将/usr/share/easy-rsa/3/所有内容复制到/etc/openvpn/easy-rsa

# 除此之外，我们想要生成的证书有组织名等等，就需要复制另外一个文件（这不是必须的，可直接跳过）
cp /usr/share/doc/easy-rsa/vars.examples /etc/openvpn/easy-rsa/vars
cd /etc/openvpn/easy-rsa
# 可以使用vim查看一下这个文件，这里就直接编辑了，我们仅聚焦于这里
cat << EOF >> vars
set_var EASYRSA_REQ_COUNTRY    "CN" # 国家
set_var EASYRSA_REQ_PROVINCE   "Jiangsu" # 省份
set_var EASYRSA_REQ_CITY       "Nanjing" # 城市
set_var EASYRSA_REQ_ORG        "University" # 组织
set_var EASYRSA_REQ_EMAIL      "me@example.net" # 电子邮件
set_var EASYRSA_REQ_OU         "Student" # 组织单位
# set_var EASYRSA_KEY_SIZE	   2048	# 密钥长度，默认就好
# set_var EASYRSA_CA_EXPIRE      3650 # CA证书有效期，天数，默认就好
# set_var EASYRSA_CERT_EXPIRE    825 # 认证有效期，天数，默认就好
EOF
# 我们就基于这个文件夹开始生成证书及密钥了

# 补充说明
./easy-rsa help [COMMAND] # 可以查看命令的帮助信息
```

## 生成证书及密钥

1.   初始化 `pki` 目录

```shell
# 在 /etc/openvpn/easy-rsa 目录下
./easyrsa init-pki
# 该目录下有 pki 文件夹，可以用tree看看目录结构，有一些预设文件和目录
tree pki
pki
├── openssl-easyrsa.cnf
├── private
├── reqs
└── safessl-easyrsa.cnf
```

2.   创建根证书，上述的背景知识可以知道，根证书非常重要是验证CA、服务端甚至客户端的一个证书，之后的服务端和所有客户端都需要这个证书

```shell
./easyrsa build-ca

> Enter New CA Key Passphrase:  # 提示你输入这个ca的密码，后续都需要这个密码来签名证书的时候进行验证，密码不能太短，请记住 CA验证密码
> Re-Enter New CA Key Passphrase: # 再次输入

Common Name (eg: your user, host, or server name) [Easy-RSA CA]: # 提示你输入CA的名字，比如yutanCA
tree pki  # 继续看一下有哪些文件了
pki
├── ca.crt  # 这个就是 ca 根证书了，后续需要保存到客户端
├── certs_by_serial
├── index.txt
├── index.txt.attr
├── issued
├── openssl-easyrsa.cnf
├── private
│   └── ca.key  # ca的私钥
├── renewed
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── reqs
├── revoked
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── safessl-easyrsa.cnf
└── serial
```

3.   生成服务端公私钥，并签名

```shell
# ./easy-rsa gen-req <filename_base> [cmd-opts] # filename_bash留意一下，就是密钥文件的文件名（不含拓展名）
./easyrsa gen-req  server nopass  # nopass表示不用密码加密私钥，默认是加密的

Common Name (eg: your user, host, or server name) [server]:  # 提示输入服务器名称 比如 yutanServer

# ./easy-rsa sign <type> <filename_base> # type表示类型（client server serverClient ca），filename_base就是基础文件名，上面设置成server
./easyrsa sign server server # 对server的公钥文件使用server类型的签名
Confirm request details: # 输入yes确认下一步
Enter pass phrase for /root/others/easy-rsa-test/pki/private/ca.key:  # 输入第2步设置的 CA验证密码

# 再查看一下文件树
tree pki
pki
├── ca.crt  # ca 证书
├── certs_by_serial
│   └── 053585F384F3B3AA5BD6702D708AC461.pem
├── index.txt
├── index.txt.attr
├── index.txt.attr.old
├── index.txt.old
├── issued
│   └── server.crt  # 服务器证书
├── openssl-easyrsa.cnf
├── private
│   ├── ca.key  # ca私钥
│   └── server.key # 服务器私钥
├── renewed
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── reqs
│   └── server.req
├── revoked
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── safessl-easyrsa.cnf
├── serial
└── serial.old
# 可能想问，ca公钥和服务器公钥在哪呢？其实就在证书里面
openssl x509 -in <crt file> -noout -text # 查看证书文件
```

4.   生成 `Diffie-Hellman` 密钥交换算法的文件

```shell
./easyrsa gen-dh

# 查看文件树
tree pki
pki
├── ca.crt
├── certs_by_serial
│   └── 053585F384F3B3AA5BD6702D708AC461.pem
├── dh.pem  # dh文件
├── index.txt
├── index.txt.attr
├── index.txt.attr.old
├── index.txt.old
├── issued
│   └── server.crt
├── openssl-easyrsa.cnf
├── private
│   ├── ca.key
│   └── server.key
├── renewed
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── reqs
│   └── server.req
├── revoked
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── safessl-easyrsa.cnf
├── serial
└── serial.old
# 可用下面命令查看
openssl dhparam -inform <DER|PEM> -in <dh.pem file> --noout -text
```

5.   生成客户端证书并签名，也可以在客户端完成，但是需要拷贝一些文件，所以推荐直接在服务端完成即可，同第3步差不多

```shell
./easyrsa gen-req client nopass # 同3.
./easyrsa sign client client # 同3.

# 如果需要签署更多的客户端
./easyrsa gen-req client2 nopass
./easyrsa sign client client2
...

tree pki
pki
├── ca.crt # ca证书
├── certs_by_serial
│   ├── 053585F384F3B3AA5BD6702D708AC461.pem
│   └── BB4FD56FB66FCCF0AA0550F57D4F5FC0.pem
├── dh.pem # dh算法文件
├── index.txt
├── index.txt.attr
├── index.txt.attr.old
├── index.txt.old
├── issued 
│   ├── client.crt # 客户端证书
│   └── server.crt # 服务端证书
├── openssl-easyrsa.cnf
├── private
│   ├── ca.key # ca私钥
│   ├── client.key # 客户端私钥
│   └── server.key # 服务端私钥
├── renewed
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── reqs
│   ├── client.req
│   └── server.req
├── revoked
│   ├── certs_by_serial
│   ├── private_by_serial
│   └── reqs_by_serial
├── safessl-easyrsa.cnf
├── serial
└── serial.old
```

6.   生成`ta.key`，这个仅仅在需要使能`openvpn`的`tls-auth ta.key 0`时候使用，作用是`HMAC firewall`的加密密钥，也就是对整个数据哈希后对哈希值加密，形成（数据+加密哈希值），需要给拷贝给客户端

```shell
cd /etc/openvpn/server
openvpn --genkey --sercret ta.key # 注意这里用的openvpn生成的
```

7.   提取有用文件

```shell
# 服务端需要的文件
pki/ca.crt  # ca的证书，包含ca的公钥
pki/issued/server.crt # 服务端证书，包含服务端的公钥
pki/private/server.key # 服务端的私钥
pki/dh.pem # dh算法文件
ta.key

# 客户端需要的文件，拷贝到linux的/etc/openvpn/client或者windows的./config/或者C:\\User\<用户名>\OpenVPN\(其中./表示安装目录)
pki/ca.crt # ca证书，包含ca的公钥
pki/issued/client.crt # 客户端证书，包含客户端的公钥
pki/private/client.key # 客户端私钥
ta.key

# 客户端2需要的文件，拷贝到linux的/etc/openvpn/client或者windows的./config/或者C:\\User\<用户名>\OpenVPN\(其中./表示安装目录)
pki/ca.crt
pki/issued/client2.crt
pki/private/client2.key
ta.key

...

# 至于为什么客户端不需要服务端的证书（或公钥），因为连接的时候会交换各自的证书给对方
```

## 生成配置文件

配置文件模板可以在`/usr/share/doc/openvpn/sample/sample-config-files`下看到，有`server.conf`和`client.conf`两个文件，`window`客户端的模板文件还可以看`/usr/share/doc/openvpn/sample/sample-windows/sample.ovpn`，这里给出配置文件及解释，详细解释可以参考[服务端配置文件解释](https://www.vsay.net/mycode/142.html)和[客户端配置文件解释](https://blog.51cto.com/TaoSec/5201779)

下面给出客户端的配置文件

```yaml
# server.conf
port xxxx  # 端口
proto tcp  # 支持tcp协议
proto udp  # 支持udp协议
dev tun    # 采用tun模式，tap模式表示数据链路层协议传递MAC帧，tun模式表示网络层协议传递IP数据报
ca /etc/openvpn/easy-rsa/pki/ca.crt                # 指定ca证书
cert /etc/openvpn/easy-rsa/pki/issued/server.crt   # 指定服务器证书
key /etc/openvpn/easy-rsa/pki/private/server.key   # 指定服务器私钥
dh /etc/openvpn/easy-rsa/pki/dh.pem                # 指定dh密钥交换算法文件
topology subnet								# 网络拓扑结构采用子网的形式，除非Windows客户端是 v2.0.9或者更低版本
server 192.168.0.0 255.255.255.0			# 子网网段和掩码，任何私钥ip地址段都可以
ifconfig-pool-persist ipp.txt				# 指定客户端ip的绑定信息，用于重启服务端或者重启客户端保证客户端ip不变
push "route 192.168.0.0 255.255.255.0" 	# 推送路由信息去客户端，还可以把客户端的路由信息也写上去，其他客户端就可以访问某个客户端的内网了
push "route 172.17.0.0 255.255.0.0" 		# 172.17.0.0 是我服务器docker的网段，做了个演示，客户端可访问服务器的docker内网
client-to-client							# 客户端可以得知客户端存在
duplicate-cn					# 客户端的私钥，证书可相同
keepalive 10 300				# 每10秒发送数据包判断客户端是否存活，300秒没收到存活信号判定为断开连接
tls-auth ta.key 0				# HMAC，参考上面 生成证书及密钥 的 第6点
cipher AES-256-CBC				# 加密方式
max-clients 50					# 最大客户端
user vpnuser					# 降低用户权限，服务启动后，将守护进程的权限降低而不是继续用root，安全，需要有这个用户
group vpnuser					# 降低用户组权限，服务启动后，将守护进程的组权限降低而不是继续用root，安全，需要有这个用户组
persist-key						# 持久化密钥连接，防止重连用户权限降低导致的某些问题
persist-tun						# 持久化tun模式的连接，防止重连用户权限降低导致的某些问题
status openvpn-status.log		# 状态log存储文件，每分钟清空
log         openvpn.log			# log存储文件，log表示新建log文件，log-append表示追加到log
verb 3							# log等级
mute 10							# 重复消息的默认，重复的10条消息会写入log，重复超过就不写入
explicit-exit-notify 1			# 告诉客户端，服务端在重启，让其可以重连
```

下面再给出客户端的配置文件

```yaml
# client.conf
client  # 表示客户端
dev tun # tun模式
proto tcp  # 支持tcp
proto udp  # 支持udp
remote [ip地址] [端口]   # 服务端ip和端口，可指定多个，后续可以使用remote-random来随机连接到其中一台
resolv-retry infinite  	# 断开会自动重连
nobind 				   	# 不绑定客户端的端口
persist-key				# 持久化密钥连接，防止重连用户权限降低导致的某些问题
persist-tun				# 持久化tun模式的连接，防止重连用户权限降低导致的某些问题
mute-replay-warnings	# 忽略重复数据包的警告信息
ca ca.crt				# ca证书
cert client.crt			# 客户端证书
key client.key			# 客户端私钥
tls-auth ta.key 1		# HMAC密钥，服务端设置成0，客户端必须设置成1
cipher AES-256-CBC		# 加密方式
verb 3					# log等级
mute 10					# 重复消息的默认，重复的10条消息会写入log，重复超过就不写入
```

## 启动服务

`linux`的服务文件在`/lib/systemd/system/opencv-server@.service`和`/lib/systemd/system/opencv-client@.service`，在这两个文件中是指定了目录的。我们查看一下。

```shell
# openvpn-server@.service
WorkingDirectory=/etc/openvpn/server # 所以我们刚刚的ta.key需要放到这里

# openvpn-client@.service
WorkingDirectory=/etc/openvpn/client # 所以我们刚刚的ca.crt, client.crt, client.key, ta.key都需要放到这里

# 值得注意的是，客户端服务端都有 %i.conf 这也说明，我们可以启动多个服务，并且指定多个文件
```

下面开始启动服务

```shell
# 服务端
systemctl start openvpn-server@server.service # 配置文件必须是server.conf
# systemctl start openvpn-server@server2.service # 配置文件必须是server2.conf

# 客户端
systemctl start openvpn-client@client.service # 配置文件必须是client.conf
# systemctl start openvpn-client@client2.service # 配置文件必须是client2.conf
```

在`windows`下，直接启动`GUI`程序即可，把`ca.crt, client.crt, client.key, ta.key, client.conf`拷贝到`./config/`或者`C:\\User\<用户名>\OpenVPN\`下即可（`./`表示`openvpn`的安装目录），然后右键托盘图标并连接即可。

# 参考文献

[^1]: 维基百科编者. TUN与TAP[G/OL]. 维基百科, 2021(20211104)[2021-11-04]. [https://zh.wikipedia.org/w/index.php?title=TUN%E4%B8%8ETAP&oldid=68519040](https://zh.wikipedia.org/w/index.php?title=TUN与TAP&oldid=68519040).



