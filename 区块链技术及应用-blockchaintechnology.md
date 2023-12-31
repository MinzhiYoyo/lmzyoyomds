---
title: 区块链技术及应用
date: 2023-06-24 16:35:35.0
updated: 2023-06-30 01:03:44.662
url: /archives/blockchaintechnology
categories: 
- 区块链
tags: 
- 比特币
- 以太坊
- 区块链
---

# 第一章 基础

- 炒币有风险，投资需谨慎
- 区块链≠比特币 && 学习区块链≠学习炒币
- 该[教程](https://www.bilibili.com/video/BV1Vt411X7JF)学自北大肖臻研究员的公开课《区块链技术与应用》，可能需要有些区块链的基础知识才能看懂该文章，区块链基础知识可以参考Tans的[文章](https://tans.fun/archives/qu-kuai-lian-ru-men)或者[李永乐](https://www.bilibili.com/video/BV1Bb411B7dq)的视频。

## 前置知识

&emsp;需要你有 **数组**，**链表**，**二叉树**，**哈希函数**，**密码学**等方面的知识，基本算法，一定编程经验。

## 参考资料

- BitCoin and Cryptocurrency Technologies A Comprehensive Introduction（中英文版本）
- 以太坊白皮书、黄皮书、源代码
- Solidity文档

## 关键词

***注意：*** 下述很多单词目前没有官方的翻译，没有官方翻译的单词用括号表示，全是编者自己写的，在分号后面给出解释。

- cryptocurrency：加密货币；
- cryptographic hash function：密码哈希函数；
- collision resistance：抗碰撞；实际上代表没有好办法进行人为碰撞，只能用蛮力求解，目前也无法证明一个哈希函数的collision resistance，只能通过经验得到
- message digest：消息摘要；
- hiding：(隐藏)；指无法从哈希的输出推出输入，也就是输入信息是隐藏的
- digital commitment / digital equivalent of a sealed envelope: 数字承诺/密封信封的数字等价物；Alice做出一个预测，并公布该预测的哈希函数输出，等到兑现预测那天再公布预测，并由世人检查该公布的预测哈希是否符合公布的哈希输出。
- puzzle friendly：(迷惑友好)；无法控制输入使得输出落在某区间，也就是无法得知哪种输入更好算出某些特定的输出
- proof of work：工作量证明；挖矿过程中需要对工作量进行证明
- target space：（目标空间）；要求输出指定目标空间范围的哈希值
- difficult to solve, but easy to verify：（难解易证）；很难去解决某个问题，但是很容易验证某个答案是否正确，在比特币设置题目的时候需要符合该特征
- (a)symmetric encryption algorithm：(非)对称加密算法；
- public key / private key：公钥/私钥；非对称加密中学到过的
- a good source of randomness：一个好的随机源；能够产生很好效果的随机源
- hash pointers：哈希指针；保存结构体的地址+哈希值。只适用于**无环**
- genesis block：创世区块；区块链中第一个区块。
- most recent block：最近的区块；区块链的最后一个区块
- tamper-evident log：（防篡改日志）；假如区块链中某个区块的内容被篡改，则该块的后续所有的哈希指针都需要更改，然后每个用户只记录most recent block的哈希，则能够证明该区块链是否被更改。
- Merkle tree：默克尔树；相比于binary tree(二叉树)来说，使用哈希指针代替普通指针。并且只有最底层节点是data block，其他节点都是只包含两个哈希指针的节点，根节点同样包含两个哈希值，根节点的两个哈希值组合起来可以作为区块链中一个block的哈希值。因此只需要记录根节点就可以快速验证整个数据块，并且查询的时间是$O(lgn)$级别的。用途是提供Merkle proof。
- data block：数据块；注意跟区块链中的block区分开来，若干个data block组成Merkle tree的最底层节点。也称之为 `tx`，`tx`也就是一次交易。
- Block Chain：区块链；由一个一个区块组成的链，每个block分为block header和block body，block header包含Merkle tree中根节点的两个哈希值
- Merkle proof：默克尔证明；比特币有两种节点，全节点和轻节点。全节点有整个block，但是轻节点只有block header。Merkle proof表示从待证明的tx到根节点的所有未知的哈希值，如图2-1红色所示 
- proof of membership/inclusion：（成员关系/录入证明）；Merkle proof可以证明Merkle tree里包含某个交易，该过程也称之为此，时间复杂度为$O(lgn)$
- proof of non-membership：（无录入证明）；与上述相反，使用Merkle proof证明Merkle tree不包含某个交易，如果叶节点排序随机，则需要$O(n)$复杂度。如果叶节点按照哈希排序，则很快能够证明。
- sorted Merkle tree：排序的默克尔树；默克尔树的叶节点按照哈希值排序，比特币中没有用到。
- double spending attack：花两次攻击；指的是如果一种货币可以很轻松被复制（如数字货币，可以轻易复制但很难更改），那么可以复制一份来花。
- BitCoin Script：比特币脚本；比特币每次交易的输出和输入都是脚本，分别成为输出脚本和输入脚本。
- full node / light node：全节点/轻节点；系统中有两种节点，全节点又叫做fully validating node。全节点参与区块链的构造和维护，轻节点只能使用区块链。
- distributed consensus：分布式共识；
- distributed hash table：分布式哈希表；
- impossibility result：不可能结论；分布式系统里的一个概念，最著名的是FLP，在异步系统(asynchronous)中，只要有一个成员是faulty的，那也无法达成共识。
- CAP Theorem： Consistency，Availability，Partition tolerance理论；只能满足三个中的两个性质。
- Paxos：（）；分布式中比较注明的一个协议
- hyperledger fabric：（超级账本结构）；有些区块链并非所有人都可以访问和使用的，这个是一种非公开的区块链
- sybil attack：女巫攻击；某个节点试图构建多个账户来投票，类似于刷票行为
- longest valid chain：最长合法链；区块链中接受的区块应该是最长合法链的区块，用于抵御分叉攻击
- forking attack：分支攻击；通过往区块链中插入区块来篡改区块链
- orphan block：孤块；除了最长合法区块链之外，其他的短分支都会被丢弃，则那些块成为孤块。
- coinbase transaction：（币库交易）；唯一产生新的比特币的途径，不需要指明交易来源。在一开始，每一个区块能够产生50BTC，协议中规定21w个区块后，之后每个区块产生的比特币必须减半，平均出块时间是10分钟，约4年时间出块奖励减半。
- mining：挖矿；比特币争夺记账权称为挖矿，miner称为矿工
- transaction-based ledger：交易式账本；比特币记账是一种基于交易的账本，对隐私保护好，但是需要对交易中的比特币进行溯源。
- account-based ledger：账户式账本；以太坊记账是一种基于账户的账本
- UTXO： Unspent Transaction Output，未交易输出；比特币中维护的一个数据结构
- transaction fee：交易费用；交易的小费
- Bernoulli trial, a random experiment with binary outcome：伯努利试验，具有二元结果的随机实验；
- Bernoulli process, a sequence of independent Bernoulli trials：伯努利过程，一系列独立的伯努利实验；具有无记忆性(memoryless)
- Poisson process：泊松过程；
- exponential distribution：指数分布；整个系统的出块时间则会服从指数分布
- progress free：；由于挖矿是无记忆性的，否则强算力的矿工会在未来会有更大的优势挖到矿。
- confirmation：确认；某区块写入区块链中需要等待确认，比特币设置为6个。
- selfish mining：；
- ghost：；以太坊的共识协议
- pool manager：矿池；挖矿中的分为矿池和矿工
- almost valid block：（近乎有效的区块）；指矿池给矿工分配的难度，比真正的区块难度低，用来证明矿工的工作量。
- AltCoin： Alternative Coin（替代硬币）；有些小币种需要消耗掉一些比特币才能获得
- state fork：（状态分歧）；由于对比特币当前状况有分歧的分叉
- deliberate fork：（故意分叉）；是故意的进行分叉，指的是分叉攻击
- protocal fork：（协议分叉）；协议更改导致的分叉
- hard fork：硬分叉；看第二章的分叉
- soft fork：软分叉；看第二章的分叉
- MULTISIG：多重签名；
- smart contract：智能合约；
- externally owned account：外部账户；
- smart contract account：合约账户；
- trie：来源于retrieval；翻译成中文是字典树或者前缀树。
- Patricia trie：压缩前缀树；经过路径压缩的前缀树。
- bloom filter：布隆过滤器；这是一个数据结构，可以在大集合中快速查找。只能检测一定不在里面和可能在里面，但是不支持删除操作。
- uncle block：以太坊中的分叉；
- bug bounty：漏洞赏金；
- ASIC resistance： ASIC阻抗性；阻止对ASIC芯片的利用，一个常用的办法就是增加内存，因为ASIC芯片利用内存性能若，memory hard mining puzzle，一个早期例子是莱特币(LiteCoin)，其用基于scrypt的哈希函数。scrypt设计思想是，开设一个很大的数组，按照顺序填充伪随机数，后面每一个位置都是前一个取哈希的值得到。
- IPO： Initial Public  Offering，首次公开募股；
- ICO： Initial Coin Offering，首次代币发行；

## 大纲

- 比特币
    - 密码学基础
    - 比特币的数据结构
    - 共识协议和系统实现
    - 挖矿算法和难度调整
    - 比特币脚本
    - 软分叉和硬分叉
    - 匿名和隐私保护
- 以太坊
    - 概述：基于账户的分布式账本
    - 数据结构：状态树、交易树、收据树
    - GHOST协议
    - 挖矿：memory-hard mining puzzle
    - 挖矿难度调整
    - 权益证明
        - Casper the Friendly Finality(FFG)
    - 智能合约

# 第二章
见[区块链技术与应用2](https://lmzyoyo.top/archives/blockchaintechnology2)

# 第三章
见[区块链技术与应用3](https://lmzyoyo.top/archives/blockchaintechnology3)


# 第四章 Solidity

## 介绍

&emsp;可以查看[官方教程](https://docs.soliditylang.org/zh/v0.8.19/)，编译器集成开发环境采用[Remix IDE](https://docs.soliditylang.org/zh/latest/installing-solidity.html)。本文只对`Solidity`做一个简单的入门教程，并且姑且认为读者具有Java、C/C++、Python、JavaScript等编程语言的基础。

&emsp;与`Java`运行于`JVM`相类似，`Solidity`运行在`EVM`上。`EVM`是提供基本的指令集，且最长寻址位为256，因此`EVM`基本数据类型中最长数据类型所占比特数为256。

## 开始

下面是一个简单的例子，将介绍一个最基本的`Solidity`语言需要包括哪些例子与一些基本的结构，该文件保存为 `hello.sol`。注意，该例子并没有实际的意义，仅仅作为教学而使用。

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.21 <0.9.0;
/**
 * 这是一种多行注释写法
*/
constract SimpleExample{
	uint count;
	address host;
	mapping(address => uint) balances;
	
	struct User{
		uint weight;
		bool voted;
	};
	
	constructor(uint i){
		count = i;
		host = msg.sender;
	}
	
	function add(uint i) returns (uint) {
		require(count == msg.sender, "Only the host can call this function!");
		count += i;
		return count;
	}
	
	function bid() public payable {
		emit bidOccurred(12);
	}
	
	event bidOccurred(uint increase);
	error contractEnded(uint time);
}
```

- `// SPDX-License-Identifier: GPL-3.0`：这是声明该合约遵循的开源协议，编译器会读取这一行。由于区块链上所有内容任何人都可以获取，合约代码同样也不例外。因此，合约代码作为开源代码需要添加开源协议。
- `pragma solidity >=0.4.21 <0.9.0;`：这是告诉编译器该合约代码需要的版本，为了兼容性而设置。
- `constract SimpleExample{ ... }`：定义了一个`constract`，姑且把它认为是其他编程语言中的`class`
- `constract`内部的代码，将在下一节展开讲

## 数据结构

&emsp;我们仍然使用上一节中的`SimpleExample`例子，这一节将会介绍上述出现的常用数据类型以及结构。本教程的内容将比官网的介绍缩减不少，更多详解请查看官网[数据类型](https://docs.soliditylang.org/zh/latest/types.html#)章节。

- `uint`：这表示一个无符号整数，此外，还有`uint256`等等。更多关于整数类型的介绍，请查看官网[整型](https://docs.soliditylang.org/zh/latest/types.html#integers)
- `address`：这是一个地址类型，为`Solidity`预设的基本数据类型，保存着一个以太坊地址，该地址占20个字节。[地址类型](https://docs.soliditylang.org/zh/latest/types.html#address)
- `mappint(KeyType => ValueType)`：表示一个[映射类型](https://docs.soliditylang.org/zh/latest/types.html#mapping-types)，与其他编程语言中的`map`类似，是`KeyType`到`ValueType`的一个映射。
- `struct`：表示定义一个结构体，可以类比于C语言的结构体。
- `constructor`：表示该`constract`的构造函数，并且只在该合约创建时执行一遍。由区块链的知识可以得知，合约是作为交易的data域写入区块链的，写入时会执行一遍合约，此时将会执行一遍`constructor`函数。
- `function [name]([parameters list]) [Modifier] [returns (returnDataType)] { function body }`：这是一个函数定义的基本结构
    - 其中大部分函数的名字将不会省略，除了一些特殊的函数，如`fallback()`函数；
    - `Modifier`代表一些对函数的修饰；
    - `[returns (returnDataType)]`表示声明返回类型，注意其中 `returns`中是有一个`s`的；
    - `{function body}`表示函数体；
    - 整个函数的结构与`JavaScript`函数定义非常相似，但在一些细节地方有所区别。
- `event eventName(parameters list);`表示一个事件，如果使用`emit eventName(parameters list);`可以发送一个事件，在区块链上与之相关的区块都会收到该事件。
- `error errorName(parameters list);`表示一个事件，如果使用`revert errorName(parameters list);`可以终止代码的执行，并且复原到原始状态，并返回错误给调用者。

# 第五章 区块链的应用

**问**：保险放到区块链上，因为区块链只需要一个小时转账。

答：保险理赔慢不是因为转账慢，而是理赔内容需要人工审核。这方面，区块链无法加速。



**问**：区块链做防伪溯源。

答：把蔬菜所有环节都放到区块链上，区块链无法检测线下的欺骗，本身写入区块链是假内容。



**问**：互不信任实体进行共识本身是伪命题。

答：区块链无法进行线下的信任，比特币只是支付方式，但是商业模式并不是去中心化的，所以很多东西都是中心化与去中心化相结合的。



**问**：区块链的不可篡改性

答：共识协议没有设定更改方式会导致退款等功能无法实现，辨析：退款不属于回滚交易，只是增加一笔交易，比特币也是可以达到同样的效果。



**问**：法律监管与保护，缺乏监管，不受中心化的干涉

答：没有监管并不一定是好的，有时候是需要中心化的监管与保护，所以中心化既有监管，也有保护。从另一个角度说，法律上的监管与保护跟支付措施无关的，而且各个国家与地区的监管与保护不一样。



**问**：所有地方都要推崇比特币

答：比特币并不是与传统货币相竞争的关系，比特币是一个很好的跨国跨地区支付方式，所以在咖啡店实行比特币支付的方式是意义不大的。信息交互很方便，但是价值交互不方便。Information can flow freely on the Internet, but payment cannot.



**问**：智能合约只有程序员看得懂

答：程序正在改变世界，Software is eating the World. 目前出现的安全漏洞，以后会有解决方案的。



**问**：民主一定是正确的吗，丘吉尔说过： Democracy is the worst form of Government except for all those other forms that have been tried from time to time.

答：民主不是最好的，但也不是最坏的。集中不是最好的，但也不是最坏的。多数人不一定代表正确性。不能跟风去中心化，并不是去中心化一定就是好的。

# 第六章 总结

&emsp;学习技术，学习思想，不学习炒币。去中心化与中心化，民主与集中。不应该有谁对谁错。

