# 使用RSA

![img](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250303181626661.png)

1.   TLS一次握手：`Client Hello`，发送TLS版本，客户端随机数，密码套件（`密钥交换算法_签名算法_WITH_对称加密算法_摘要算法`组成）
2.   TLS二次握手：
     1.   `Server Hello`：确认TLS版本，服务器随机数，选择的密码套件，如`TLS_RSA_WITH_AES_128_GCM_SHA256`
     2.   `Server Certificate`：服务器证书，包括公钥，元数据
     3.   `Server Hello Done`
3.   TLS三次握手：
     1.   客户端[校验证书](https://lmzyoyo.top/archives/openvpnserverandclient)就，生成预主密钥（Pre-master），然后生成会话密钥。
     2.   `Change Cipher Spec`：用服务器公钥加密预主密钥并发送给服务器
     3.   `Finishd`：Encrypted Handshake Message
4.   TLS四次握手：服务端解密预主密钥，然后生成会话密钥。`Change Cipher Spec`，`Finished`

基于`RSA`的密钥交换可以看出，如果服务器的私钥泄露，那么所有之前的会话密钥都可以被获取，所以不安全（不支持前向安全）

# 使用 ECDHE

下图是基于离散对数的密钥交换（DH）算法

![img](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250303205808417.png)



由于其计算性能并不佳，因此可以使用椭圆曲线替代

-   双方定好哪种椭圆曲线，曲线上的基点G
-   小红生成私钥`d1`和公钥`Q1=d1*G`，小明生成私钥`d2`和公钥`Q2=d2*G`
-   双方交换公钥，最终生成共同密钥`(x, y)=d1*Q2=d2*Q1=d1*d2*G`
-   这是因为椭圆曲线满足数乘交换律



`ECDHE`的TLS四次握手如下：

1.   TLS一次握手：`Client Hello`，发送TLS版本，客户端随机数，密码套件（`密钥交换算法_签名算法_WITH_对称加密算法_摘要算法`组成）
2.   TLS二次握手：
     1.   `Server Hello`：确认TLS版本，服务器随机数，选择的密码套件，如`TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
     2.   `Server Certificate`：服务器证书，包括公钥，元数据
     3.   `Server Key Exchange`：选择曲线，如x25519；生成随机私钥并保存；根据G点计算公钥，使用RSA签名对公钥签名后发送给客户端。
     4.   `Server Hello Done`
3.   TLS三次握手：
     1.   客户端[校验证书](https://lmzyoyo.top/archives/openvpnserverandclient)
     2.   `Client Key Exchange`：生成客户端的私钥和公钥，发送给服务端；
     3.   同时，服务器客户端都可以根据`x`和服务器客户端的随机数三个东西计算出会话密钥。用三个东西的意义在于，三个伪随机可以达到随机程度更高。
     4.   `Change Cipher Spec`（意味着后续使用对称加密了）
     5.   `Finishd`（Encrypted Handshake Message）
4.   TLS四次握手：`Change Cipher Spec`，`Finished`

