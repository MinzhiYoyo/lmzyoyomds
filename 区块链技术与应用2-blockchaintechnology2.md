---
title: 区块链技术与应用2
date: 2023-06-27 21:03:44.824
updated: 2023-06-27 21:08:48.311
url: /archives/blockchaintechnology2
categories: 
- 区块链
tags: 
- 区块链
- 去中心化
---

# 第一章
见[区块链技术与应用](https://lmzyoyo.top/archives/blockchaintechnology)

# 第二章 比特币

## 比特币之密码学基础

### 哈希函数

- 哈希函数应该具有如下性质：collision resistance; hiding; puzzle friendly; difficult to solve, but easy to verify
- 如果哈希函数输入不够大，则在哈希输入后面拼接 **随机数**。
- 比特币使用SHA-256(Secure Hash Algorithm)

## 签名与账户管理

&emsp;中心化系统开账户是在服务中心申请开账户，比特币属于去中心化，则只需要本地创建公私钥对就代表开账户了。

&emsp;签名使用私钥加密，验证签名使用公钥。用户使用私钥对某交易加密，其他用户可以使用公钥对该交易进行验证。

- 提问1：如果某个不法分子产生大量公私钥对进行碰撞，看是否碰撞到某个用户的公私钥对。答案：在公私钥空间很大的情况下，这种概率太小，从理论上可以行，但是受限于计算机的算力，目前仍未发生此攻击方法成功攻击。

&emsp;签名过程：先对信息产生一个哈希，再用私钥对哈希进行签名

## 数据结构

&emsp;hash pointers区块链中的指针，非传统指针。每个区块里都包含一个hash pointer，该hash pointer的地址部分指向**前一个区块**，并且该hash pointer的哈希部分是前一个区块的**hash pointer + 内容**的哈希值。

&emsp;区块链具有tamper-evident log性质。

&emsp;Merkle proof，如何证明跟某个轻节点证明交易写入了区块链？在Merkle tree中找到该交易所在的data block，依次往上找到根节点

![图2-1 某区块链中的Merkle proof](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306242157699.png)

轻节点像全节点发送请求，全节点将红色的哈希值发送给轻节点，则轻节点可以依次往上面算。

提问：假如某个绿色哈希值错误（也就是交易被篡改），是否可以通过更改红色哈希值（红色哈希值由全节点提供，无法证明真伪）来保证再上一层的绿色哈希值不改变？答案是不行的，因为哈希算法需要具有collision resistance。

## 共识协议

&emsp;数字货币面临主要挑战就是double spending attack

&emsp;每个交易都有一次输入脚本和一次输出脚本，验证则是把上一交易输出脚本与本交易输入脚本拼接看是否能够正确执行。

&emsp;区块链中一个块分为块头和块体，记为Block header和Block body

- Block Header：
    - Version：版本信息
    - Hash of previous block header：前一个区块的哈希指针，这个哈希只包括前一个区块头的哈希
    - Merkle tree root hash：默克尔树的根哈希值，这个哈希已经保证了block body中的交易无法被篡改的
    - Target：挖矿的目标，$H(block header)\leq target$
    - Nonce：随机数
- Block body：
    - transaction list：交易链表

&emsp;比特币因为是去中心化的，需要解决有恶意的节点问题，但是恶意节点是少量。比特币使用算力投票机制，来应对这种情况与女巫攻击。

&emsp;最长合法链，可抵御分支攻击。

## 系统实现

&emsp;比特币还会维护一个UTXO数据结构

![图2-1 块例子](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306252023528.png)

上图为某块的一个例子，数据来源于[blockchain.info](https://blockchain.info)。更改Block Header中的Nonce字段或者Block Body中必定有的一个CoinBase （该块产生的比特币）字段的某些值，然后计算Block Header的哈希来作为一个答案，并通过设定的难度来验证答案是否可行。

&emsp;比特币总量计算：$21w\times 50 \times(1+\frac{1}{2} + \frac{1}{4}+...) = 2100w$。比特币的计算并不是解决某个数学难题，比特币计算消耗的算力其实是无其他意义的，其唯一意义就在于维护系统的安全，只要大部分算力掌握在诚实的人手里，则安全性得到保障。BitCoin is secured by mining. 

&emsp;往往一个区块需要写入到区块链中，则需要等待6个确认区块到了之后，该区块才能写入区块链中

## 网络工作原理

&emsp;工作在应用层(application layer)：application layer(BitCoin Block chain) –> network layer(P2P Overlay Network)

&emsp;比特币网路设计原则是简单，强壮而不是高效。

&emsp;

## 挖矿难度调整

&emsp;比特币需要保证10分钟出一次块，并且每2016个区块调整一次，约14天。$target = target \times \frac{actual\quad time}{expected\quad time}$。其中，actual time是最近2016个块实际时间，expected time是20160分钟，并且最多增大4倍，最小缩小到四分之一。

![图2-3 比特币算力](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306252138410.png)

## 挖矿

CPU -> GPU -> ASIC(Application Specific Integrated Circuit)

- 全节点：
    - 一直在线
    - 在本地硬盘上维护完整的区块链信息
    - 在内存里维护UTXO集合，以便快速检验交易的正确性
    - 监听比特币网络上的交易信息，验证每个交易的合法性
    - 决定哪些交易会被打包到区块里
    - 监听别的矿工挖出来的区块，验证其合法性
    - 挖矿
        - 决定沿着哪条链挖下去
        - 当出现等长的分叉时候，选择分叉
- 轻节点：
    - 不是一直在线
    - 不用保存整个区块链，只要保存每个区块的块头
    - 不用保存全部交易，只保存与自己相关的交易
    - 无法检验大多数交易的合法性，只能检验与自己相关的那些交易的合法性
    - 无法检测网上发布的区块的正确性
    - 可以验证挖矿的难度
    - 只能检测哪个是最长链，不知道那个是最长合法链

&emsp;矿工计算哈希值，矿池负责其他工作（比如打包，查看是否矿被挖到了，分配收益等等）

&emsp;矿池根据矿工的工作量进行收益分配，通过降低难度来验证矿工的工作量，比如比特币的难度是需要挖到70个0，但是矿池可以给矿工设置只需要挖到50个0即可作数，之后根据所有矿工挖到可作数的矿来进行收益分配。并且，矿池会把coinbase设置为矿池的地址，因此矿工哪怕挖出来自己发布的话，也无法获得奖励。

&emsp;假如一个矿池的算力过大，比如占到51%(低于50%也是有可能的)的算力，那么它可以发动分叉攻击、封锁攻击

## 脚本

&emsp;比特币脚本是一种基于栈的语言，并不具有图灵完备性。看下列一个关于栈的例子（并非比特币脚本）

```
push(2)
push(3)
OP_ADD
push(6)
OP_EQUAL
```

首先将2，3压入栈，然后OP_ADD运算会将栈顶两个元素弹出栈并相加之后压入栈，然后将6压入栈，OP_EQUAL会将栈顶两个元素弹出栈并计算是否相等将结果压入栈。

&emsp;下面来看一个真正的比特币交易信息的脚本

![图2-3 一个交易实例](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306261839677.png)

```json
// 上述交易的交易结构
"result": {
    "txid": "921a...dd24",  // 该交易的id
    "hash": "921a...dd24",  // 该交易的hash
    "version": 1,  // 版本
    "size": 226,   // 大小
    "locktime": 0, // 0-该交易立即生效，非0-该交易延迟多少个区块生效
    "vin": [       // 交易的输入结构，可多个
        {
        	"txid": "c0cb...c57b",     // 来自之前哪个交易的输出，之前交易的哈希值/id
        	"vout": 0,                 // 来自之前输入的哪个输出，这里表示第0个输出
        	"scriptSig": {             // 输入脚本，给出签名即可
        		"asm": "3045...0018",  // 
        		"hex": "4830...0018"   // 
        	},
        },
    ],  
    "vout": [     // 交易的输出结构，可多个
        {
            "value": 0.22684000,  // 转出的钱
            "n": 0,				 // 序号
            "scriptPubKey": {     // 输出脚本，最简单是给出public key
                "asm": "DUP HASH160 628e..d743 EQUALVERIFY CHECHSIG",  // 
                "hex": "76a9...88ac",
                "reqSigs": 1,          // 需要几个签名才能兑现
                "type": "pubkeyhash",  // 输出类型，公钥的哈希
                "addresses": [ "19z8LJkNXLrTv2QK5jgTncJCGUEEfpQvSr" ]  // 输出地址
            }
        },{
            "value": 0.53756644,
            "n": 1,
            "scriptPubKey": {
                "asm": "DUP HASH160 da7d..2cd2 EQUALVERIFY CHECHSIG",
                "hex": "76a9...88ac",
                "reqSigs": 1,
                "type": "pubkeyhash",
                "addresses": ["1LvGTpdyeVLcLCDK2m9f7Pbh7zwhs7NYhX"]
            }
        }
    ], 
    "blockhash": "0000000...002c510...5c0b",  // 这个交易所在区块的块哈希值
    "confirmations": 23,    // 这个交易已经有多少个确认信息
    "time": 1530846727,     // 该交易产生时间戳
    "blocktime": 1530846727 // 该区块产生时间戳
}
```

验证交易的合法性，需要把该交易的**输入脚本** 与 该交易指向的交易的 **输出脚本** 组合执行，执行成功才是说明交易合法。

输入输出脚本的几种形式：

- P2PK(Pay to Public Key)：输出脚本直接给出收款人的公钥

```
input script:
	PUSHDATA(Sig)     # 输入脚本给出签名
output script:
	PUSHDATA(PubKey)  # 输出脚本直接给出公钥  
	CHECKSIG  # 弹出栈顶两个元素，验证其签名
```

值得注意的是，输出脚本来源上之前交易的收款人，输入脚本来源于当前交易的付款人，这两个是同一个人，因此公私钥对都是该人的。

- P2PKH(Pay to Public Key Hash)：输出脚本只给出公钥哈希，输入脚本既要给出签名还要给出公钥，最常用

```
input script:
	PUSHDATA(Sig)           #  栈为: Sig
	PUSHDATA(PubKey)        #  栈为: Sig PubKey1
output script:
	DUP  # 栈顶元素赋值一遍  # 栈为：Sig PubKey1
	HASH160  # 弹出栈顶元素取哈希再压入栈       # Sig PubKey1 PubKey1Hash
	PUSHDATA(PubKeyHash)  # 压入公钥哈希       # Sig PubKey1 PubKey1Hash PubKey2Has
	EQUALVERIFY  # 弹出栈顶两个元素，看是否相等 # Sig PubKey1
	CHECKSIG
```

值得注意的是，输入脚本给的公钥是当前交易付款人的公钥，输出脚本压入的公钥是之前交易收款人的公钥

- P2SH(Pay to Script Hash)：输出脚本给出收款人给的脚本哈希，付款是需要给出脚本和脚本所需签名，最复杂的一种。这个是多重签名而出的，也就是输入需要验证五个人的签名。并且，由于这个地方实现的bug，需要在输入脚本前面压入一个无用数据（毕竟是分布式的，已经不好更新了）。

```
input script:
	...
	PUSHDATA(Sig)
	...
	PUSHDATA(serialized redeemScript)
output script:
	HASH160
	PUSHDATA(redeemScriptHash)
	EQUAL
```

- Proof of Burn：销毁比特币的一种方法。一可以用于生成AltCoin，也就是AltCoin必须以消耗掉比特币为代价。二是为了往区块链中添加永久需要保存的内容，比如digital commitment，比如将知识产权的哈希放到RETURN后面。

```
output script
	RETURN  # 返回错误，后面将不会执行
	...
```

这个东西就是为了通过销毁少量的比特币的代价，往区块链中写入信息，这是任何人都可以的不需要记账权。Coinbase只有记账权的人才可以往这里面写。这个是当前交易的输出脚本，所以验证当前交易验证过程中不会执行该脚本。

## 分叉

&emsp;分为两种分叉，一种是state fork，另一种是protocal fork，其中第二种分为hard fork和soft fork。

&emsp;对于hard fork，假如把区块大小限制由1M更新到4M，且大多数算力更新到4M了，少数算力仍然保持1M。这会导致，新算力认可新链和旧链，旧算力只认可旧链，但是新算力强于旧算力，则新链和旧链可能势均力敌。导致分叉一直存在的。因此，最后会导致两帮人分家，比特币分开了，如果不采取措施其实是成为两个币种，两个币种争夺正统。之后采取措施是，为两个分支分别赋予一个chain ID。

&emsp;对于soft fork，假如把区块大小限制由1M更新到0.5M，且大多数算力更新到了0.5M，少数算例仍然保持1M。这会导致，新算力只认可新链，旧算力认可新链和旧链，但是新算力强于旧算力，则新链会越来越强。慢慢的，分支会回归到新链中。最著名的例子：P2SH。

## 匿名性

真应该好好看看P12 12-BTC-匿名性

零知识证明：一方（证明者）向另一方（验证者）证明一个陈述是正确的，而无需透露除该陈述是正确的外的任何信息。

同态隐藏：

- 如果x,y不同，那么它们的加密函数值E(x), E(y)也不相同。
- 给定E(x)的值，很难反推出x的值。
- 给定E(x)和E(y)的值，我们可以很容易地计算出某些关于x,y地加密函数值。
    - 同态加法：通过E(x)和E(y)计算出E(x+y)地值
    - 同态乘法：通过E(x)和E(y)计算出E(xy)地值
    - 扩展到多项式

盲签方法：

- 用户A提供SerialNum，银行在不知道SerialNum地情况下返回签名Token，减少A地存款
- 用户A把SerialNum和Token交给B完成交易
- 用户B拿SerialNum和Token给银行验证，银行验证通过，增加B的存款
- 银行无法把A和B联系起来
- 中心化

零币和零钞：

- 零币和零钞在协议层就融合了匿名化处理，其匿名属性来自密码学保证。
- 零币(zerocoin)系统中存在基础币和零币，通过基础币和零币的来回转换，消除旧地址和新地址的关联性，其原理类似于混币服务。
- 零钞(zerocash)系统使用zk-SNARKs协议，不依赖一种基础币，区块链中只记录交易的存在性和矿工用来验证系统正常运行所需要关键属性的证明。区块链上既不显示交易地址也不显示交易余额，所有交易通过零知识验证的方式进行。

## 思考

**问**：哈希指针中地址存储是什么？

答：实际系统中，只有哈希，没有指针。全节点一般使用levelDB中使用(key, value)来存储，key为哈希，value为区块的内容或实际地址。



**问**：区块恋

答：设想有一对情侣创建一个账号，然后把账号截断，一人记着一半。如果两个人不在一起，那么就永远用不了这个账户，区块链连接着他们的爱情。问题是，假设256位私钥，情侣中某个人想偷钱，只需要遍历剩下的128位即可，安全性将会降低。因此，这种场景多重签名更加适用。如果他俩分手了，那么币将永远保存在UTXO里面，对矿工不友好。





**问**：分布式共识，为什么比特币系统能够绕过分布式共识中的那些不可能结论？

答：比特币没有取得真正意义上的共识，这个共识随时会被推翻，比如分叉攻击。分布式系统理论证明，异步环境中无法区分远程服务器是否垮掉或者运行缓慢，异步指的是通信传输时间没有上限。



**问**：比特币的稀缺性

答：早期挖矿难度低，奖励高。后期难度高，奖励低。稀缺的东西不适合做货币，一定好的货币需要有通货膨胀的功能，因为生产力在上升，但总量不变，类似于房价。



**问**：量子计算

答：传统金融业也是使用传统密码学，因此，传统金融业才会首当其冲。比特币中，私钥能够推导出公钥，公钥无法推出私钥。假如量子计算使得公钥推出私钥，但是比特币收款是用的是公钥的哈希，付款才是用的公钥明文。比特币中，最好每次交易都是用一个地址，将所有金钱转出来，然后将价钱转给收款人，余下的部分转给自己的另一个账户。

# 第三章
见[区块链技术与应用3](https://lmzyoyo.top/archives/blockchaintechnology3)
