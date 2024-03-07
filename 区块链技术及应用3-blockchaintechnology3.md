---
title: 区块链技术及应用3
date: 2023-06-27 21:07:02.887
updated: 2023-06-29 02:38:23.171
url: /archives/blockchaintechnology3
categories: 
- 区块链
tags: 
- 以太坊
- 区块链
---

# 第一章
见[区块链技术与应用](https://lmzyoyo.top/archives/blockchaintechnology)

# 第二章
见[区块链技术与应用2](https://lmzyoyo.top/archives/blockchaintechnology2)

# 第三章 以太坊

## 前言

&emsp;以太坊相比于比特币更改，一是mining puzzle，memory hard型，也就是内存消耗大，不太适合用专业芯片；二是出块时间；三是将来会工作量证明(proof of work)更改为权益证明(proof of stake)；四是以太坊增加了对智能合约(smart contract)思考，bitcoin: decentralized currency，ethereum: decentralized contract，比特币最小单位是一聪(satoshi)，以太坊最小单位是一wei，以太坊去中心化合约的思想是：将合约写入区块链以达到谁都不可以篡改的目的。

## 账户

&emsp;以太网是基于账户的，而比特币是基于交易的。并且比特币的交易，必须全部把所有比特币用掉，因此多的比特币要通过新建额外账户来接收，以太坊会维持一个状态树来记录各个账户的余额，它对double spending attack有天然防御作用，但是很容易受到replay attack。

&emsp;以太坊分为外部账户和合约账户，外部账户是相当于公私钥对，也称为普通账户，有账户余额(balance)和计数器(nonce)。合约账户不是通过公私钥对，除了有balance和nonce之外，合约账户可以调用另一个合约，合约账户无法发起交易。合约账户还有代码(code)和状态(storage)。

## 数据结构

&emsp;以太坊使用状态树(trie)来作为其账户数据结构，称之为MPT（Merkle Partricia tree），要实现账户地址向账户状态映射，以太坊账户地址是160bits=20B。当键值（地址）分布比较稀疏，路径压缩比较好。下图是一个简化的MPT例子。

![图3-1 简化的MPT](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306271900783.png)

下图是多个区块

![图3-2 多个区块](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306271959822.png)

下面是以太坊区块链的块头数据结构

![图3-3 以太坊区块链的块头数据结构](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272011209.png)

- Root：状态树的根哈希
- TxHash：交易树的根哈希
- ReceiptHash：收据树的根哈希
- Bloom：与收据查询相关
- Difficulty：挖矿难度
- GasLimit/GasUsed：与智能合约相关
- time：区块产生时间
- MixDigest/Nonce：与挖矿过程相关

下面是是区块的结构

![图3-4 区块的结构](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272015911.png)

- header：上述说的header的指针
- transactions：交易列表

下图是真正发布区块的信息

![图3-5 真正发布区块的结构](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272017139.png)



&emsp;上述讲得是key的存储，对于Value，则采用RLP： Recursive Length Prefix方式来序列化，其只支持一种类型——nested array of bytes（字符数组，可以嵌套）。

## 交易树和收据树

&emsp;每个区块里面的交易将会组成交易树，每个交易会形成一个收据，收据将会组成收据树，交易树与收据树的节点是一一对应，都是MPT（Merkle Patricia tree）。一个用途是提供Merkle proof，二是提供支持更加复杂的查询操作。以太坊引入了bloom filter这个数据结构。

&emsp;轻节点如果需要查找某个交易，则会先查找在哪个区块，通过区块的bloom filter，然后再在具体区块的所有交易的bloom filter中查找对应的交易。

**问**：为什么要保存所有用户的状态？

答：如果只保存变动账户的状态，寻找付款方的之前状态，则需要从当前区块开始向前遍历，如果不存在就会遍历到创世块。寻找收款方的状态，则需要从当前区块向前遍历，有可能收款方是新用户，那么就要遍历到创世块。这两者会大大减慢一次交易的速度。

下图是创建区块具体实现代码

![图3-6 创建新区块具体实现代码](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272052858.png)

![图3-7 DeriveSha函数](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272055967.png)

![图3-8 Trie的结构](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272055857.png)

![图3-9 收据Receipt的结构](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272056399.png)

![图3-10 bloom相关函数](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272058931.png)

![图3-11 查找bloom的函数](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272100367.png)

## GHOST协议

&emsp;这是以太坊的共识机制，以太坊的出块速度降到十来秒。如果依然沿用比特币的共识机制，那么以太坊是非常容易产生分叉的，则会导致centralization bias（中心化优势）的问题，不需要51的算力也许就能主导区块链了。因此，以太坊采用GHOST机制，并把分叉称为uncle block，而不是orphan block，也就是予以uncle block的奖励。

&emsp;uncle block会获得 7/8 的奖励，后续区块如果包含uncle block的指针，则会额外获得 1/32 的奖励，但是最多有**两**个uncle block，也就是同一个节点最多分叉成3个。

**问**：如果uncle block大于2怎么办

答：

**问**：如果挖到了才发现有uncle block

答：uncle block不一定指的是父辈同级的区块，如果当前区块没有包含uncle block，后续区块也会包含uncle block，具体奖励看下图，最长是7代，下面的分数是奖励，uncle reward。而对于最近区块，如果包含一个uncle block获得1/32的奖励。这个是解决临时性分叉。只需要检查uncle block的挖矿是否挖到了，不需要管uncle block的交易合法性。并且只有分叉后的第一个分叉有奖励，再后面的就没有。

![图3-12 最长uncle block](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272135979.png)

&emsp;下面讲讲以太坊的哈希函数，以太坊哈希的memory hard设计是：有两个数据集，是16M cache和1G dataset，dataset是由cache生成出来，cache是由种子节点生成第一个值，接下来的都是由前一个取哈希得到。然后生成一个更大的数组dataset，要比cache大得多，cache和dataset都会慢慢增长。dataset每个元素都是从cache中依次读取256个元素，并且每次读取都会迭代。然后生成nonce，利用nonce从dataset中随机读取64次元素，每次都是读取相邻两个元素。下面是哈希算法的伪代码。

```python
# 首先生成cache
def mkcache(cache_size, seed):
    o = [hash(seed)]
    for i in range(1, cache_size):
        o.append(hash[o-1])
    return o
```

- 注意点：
    - （1）实际中，每个30000个会重新生成seed
    - （2）实际中，后一个不是简单的用前一个的hash，有额外处理
    - （3）实际中，cache的初始大小为16M，没30000个块重新生成时增大初始大小的1/128——128K

```python
# 通过cache来生成dataset中第i个元素
def calc_dataset_item(cache, i):
    cache_size = cache.size
    mix = hash(cache[i % cache_size] ^ i)
    for j in range(256):
        cache_index = get_int_from_item(mix)
        mix = make_item(mix, cache[cache_index % cache_size])
    return hash(mix)
```

- 注意点：
    - dataset叫做DAG，初始是1G，实际中每30000个块增大初始大小的1/128——8M
    - cache中第`i%cache_size`个元素生成初始mix，为了保证每个初始mix都不同，i参与生成mix
    - 多次调用这个函数，将所有的dataset都生成了
    - get_int_from_item与make_item是实际中没有的，这里是展示mix与cache_index的关系。

```python
# 生成整个dataset
def calc_dataset(full_size, cache):
    return [calc_dataset_item(cache, i) for i in range(full_size)]
```

使用上述这个函数，生成所有的dataset

```python
def hashimoto_full(header, nonce, full_size, dataset):
    mix = hash(header, nonce)
    for i in range(64):
        dataset_index = get_int_from_item(mix) % full_size
        mix = make_item(mix, dataset[dataset_index])
        mix = make_item(mix, dataset[dataset_index + 1])
    return hash(mix)

def hashimoto_light(header, nonce, full_size, cache):
    mix = hash(header, nonce)
    for i in range(64):
        dataset_index = get_int_from_item(mix) % full_size
        mix = make_item(mix, calc_dataset_item(cache, dataset_index))
        mix = make_item(mix, calc_dataset_item(cache, dataset_index+1))
    return hash(mix)
```

- 注意点：
    - 这个展现了ethash的puzzle：通过header、nonce以及DAG求出一个与target比较的值，矿工和轻节点方法是不一样的
    - 上面是矿工，下面是轻节点。区别在于，矿工要保存所有的dataset，轻节点是由临时生成需要的即可。因为，两者生成伪随机数的种子都是来源于nonce，所以情节点与矿工是生成一样的。

```python
def mine(full_size, dataset, header, target):
    nonce = random.randint(0, 2**64)
    while hashimoto_full(header, nonce, full_size, dataset) > target:
        nonce = (nonce+1)%2**64
    return nonce
```

&emsp;这是矿工挖矿的主要函数。到目前为止，以太坊的挖矿仍然是GPU为主，ASIC已经不太作为主流挖矿。矿工挖矿需要大内存，再加上需要内存还会定期增长，所以不太可能是ASIC能接受的了。并且，以太坊设计之初就打算从PoW（Proof of work）转向PoS（Proof of stake，权益证明），权益证明不是挖矿而来的。但是目前为止，仍然没有使用PoS，每次都吓唬大家以达到没人敢冒风险来做到ASIC，以太坊使用预挖矿(pre-mining)，也就是给开发人员留了很多币。与pre-mining相关的概念是pre-sale，也就是预售pre-mining的币，以获得资产来开发。

## 挖矿难度调整

&emsp;以太坊每个块都会调整难度，如下图

![图3-12 以太坊挖矿难度](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272313021.png)

![图3-13 自适应难度调整](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272314332.png)

- y与父区块的uncle数有关，如果父区块包含uncle，则y=2，否则y=1。
- 父区块包含uncle时，难度增大一个单位，因为包含uncle时新发行的货币量大，需要提高难度以保持货币发行量稳定。
- 难度降低的上限设置为-99，主要是应对被黑客攻击或者其他目前想不到的黑天鹅事件（指概率非常低，但是一旦发生影响很大的事件）。
- $H_s$ 是当前区块时间戳，$P(H)_{H_s}$ 是父区块的事件戳，均以秒为单位，并且规定 $H_s > P(H)_{H_s}$
- 以不太uncle（y=1）为例子
    - 如果出块时间是[1,8]，出块时间果断，难度调大一个单位
    - 如果出块时间是[9,17]，出块时间可以接受，难度不变
    - 如果出块时间是[18,26]，出块时间过长，难度调小一个单位

![图3-14 难度炸弹](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272323968.png)

![图3-15 以太坊的四个发展阶段](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272330880.png)

请看[GitHub](https://github.com/ethereum/go-ethereum)源代码学习

![图3-16 最难合法链](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306272340117.png)

**值得注意的是**：以太坊中其实是最难合法链，而不是最长合法链。

## 权益证明

&emsp;Proof of Stake，权益证明。工作量证明，本质也就是按照财富证明，谁越有钱，谁就更容易挖到矿。所以，权益证明则选取货币量来进行证明，谁货币多，谁则有更大的投票权。如果有人想发动51%攻击，那么就需要花大量金钱购买此种币，则会导致该币价格大量增加，这并不对该币种是一件坏事。

&emsp;早期基于权益证明的共识机制存在一个问题，如果不沿着最长合法链挖矿，在多条链都投入币来挖，反正最后只有一条链的金钱交易有效，所以无所谓哪条链胜出。现如今对于权益证明的优化是，挖出来的50个区块称为一个epoch，然后将会对这个epoch进行投票，如果超过2/3的验证者支持，这个epoch才算通过，验证者在参与之前需要交付保证金，并且如果正常作为，就会获得部分奖励，如果验证者不作为或者乱作为，则会扣除保证金。

## 智能合约

&emsp;智能合约的本质是运行在区块链的代码，由矿工执行，代码的逻辑定义了合约的内容。智能合约的账户保存了合约当前的运行状态，balance(当前余额)，nonce(交易次数)，code(合约代码)，storage(存储)。Solidity是智能合约最常用的语言，语法上与JavaScript很接近。

&emsp;下图是一个Solidity的代码，不同版本的语言，语法有所不同，contract相当于class，其中address类型是特有的数据类型，该代码例子是一个网上拍卖的例子。

![图3-17 Solidity代码](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306282136771.png)

- 数组定义：`address[] bidders;`，可以用`bidders.push(val)`来添加元素，可以用`bidders.length`来获取长度，也可以设定固定长度数组`address[1024] bidders`

- 构造函数：`constructor(parameter list) public { code block }`，只有在合约创建才会被调用

- `payable`：以太坊规定，如果一个账户接受其他账户转账，则需要标注payable。

- 外部账户如何调用智能合约：首先创建一个交易，然后接受地址为要调用的那个智能合约的地址，data域填写调用函数及其参数的编码值。如下图所示。

    ![图3-18 外部账户调用智能合约](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306282143949.png)

- 一个合约调用另一个合约中的函数：直接调用、使用call函数和使用delegatecall函数，外部账户才能发起交易，合约账户不可以发起。如果直接调用，发生错误，则两个都会回滚。使用call函数，出现错误，只有被调用会回滚。

    ![图3-19 合约直接调用合约](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306282144859.png)

    ![图3-20 call函数调用合约](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306282145328.png)

    ![图3-21 delegatecall函数](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306282148003.png)

- `fullback()`函数：定义是`function() public [payable] { code block }`，这是一个匿名函数，没有参数也没有返回值。两种情况会调用，一种是直接向一个合约地址转账而不加data，另一种是被调用的函数不存在。如果转账金额不是0(有接受转账能力)，需要生命`payable`，否则会抛出异常。合约可以没有`fullback()`，如果没有该函数，并且没有调用其他函数，则会报错。

- 智能合约创建与运行：

    - 智能合约代码写完，需要编译成`bytecode`
    - 创建合约：外部账户向0x0地址发起转账。转账金额为0(但是要支付汽油费，汽油费是给矿工记录交易的小费)，合约代码放到`data`域
    - 智能合约运行在EVM(Ethereum Virtual Machine)上，EVM寻址是256位的，比常见的32/64位要高很多。
    - 以太坊是一个交易驱动的状态机，调用智能合约的交易发布到区块链上后，每个矿工都会执行这个交易，从当前状态确定性地转移到下一个状态。

- `gas fee`汽油费

    - 智能合约是个`Turing-complete Programming Model`（图灵完备编程模型）
    - 执行合约中的指令需要收取汽油费，由发起交易的人来支付
    - EVM中不同指令消耗的汽油费是不一样的，简单指令便宜，复杂或需要存储状态的指令就贵

- 错误处理

    - 以太坊只会全部执行或者完全不执行，不会只执行一半的。中间发生错误，执行状态会回滚但是花掉的汽油费不退。
    - 智能合约中不存在自定义`try-catch`结构
    - 可以抛出错误的语句：
        - `assert(bool condition)`：条件不满足就抛出，内部错误
        - `require(bool condition)`：条件不满足就抛掉，用于输入或外部错误
        - `revert()`：终止运行并回滚状态变动

- 嵌套调用：

    - 只能合约具有原子性：执行过程出现错误，会导致回滚
    - 嵌套调用是指一个合约调用另一个合约中的函数
    - 嵌套调用发生异常，有些调用会引起连锁式回滚，有些则不会
    - 一个合约向另一个合约账户转账，没有指明调用哪个函数，仍然会引起嵌套调用，因为`fullback()`

- `gas limit`：前面的`block header`的结构中有个`gas limit`字段，这是为了以太坊限制当前区块获得最大的汽油费，而比特币同样采用了限制，但是方式是通过限制比特币区块中块所占字节最大位1M(1M不知道有没有更新)。以太坊和比特币中，交易都是用的脚本，只不过以太坊的脚本有图灵完备性，比特币的脚本并没有。以太坊每个区块都可以自己调整`gas limit`，但是跨度只能为前一个的`gas limit`的1/1024。`gas limit`是区块能够消耗汽油的上限，不是由交易的`gas limit`决定。执行错误的交易也要发布，因为需要扣除汽油费。

- `receipt`结构：全节点是先执行智能合约再挖矿，该结构有`Status`的字段确定是不是执行成功。

- 智能合约不要多线程，因为智能合约是状态机式的，需要有确定的状态转移，所以不能有多线程，也是基于需要确定的状态转移，所以智能合约不能有真正的随机数。

- 智能合约能够获取区块的信息：

    - `block.blockhash(uint blockNumber) returns (bytes32)`：给定区块的哈希——仅对最近256个区块有效而不包括当前区块
    - `block.coinbase(address)`：挖出当前区块的矿工地址
    - `block.difficulty(uint)`：当前区块的难度
    - `block.gaslimit(uint)`：当前区块`gas`的限额
    - `block.num(uint)`：当前区块号
    - `block.timestamp(uint)`：自`unix epoch`起始当前区块以秒计的时间戳

- 智能合约能够获取的调用信息：

    - `msg.data(bytes)`：完整的`calldata`
    - `msg.gas(uint)`：剩余`gas`
    - `msg.sender(address)`：消息发送者（当前调用）
    - `msg.sig(bytes4)`：`calldata`的前4个字节（也就是函数标识符）
    - `msg.value(uint)`：随消息发送的`wei`的数量，也就是以太坊币的最小单位
    - `now(int)`：目前区块的时间戳(`block.timestamp`)
    - `tx.gasprice(uint)`：交易的`gas`价格
    - `tx.origin(address)`：交易发起者（完全的调用链）

- 智能合约的地址类型

    - `<address>.balance(uint256)`：以`wei`为单位的`<address>`的余额
    - `<address>.transfer(uint256 amount)`：向`<address>`发送数量为`amount`的`Wei`，失败抛出异常，发送2300 gas的矿工费，不可调节
    - `<address>.send(uint256 amount) returns(bool)`：向`<address>`发送数量为`amount`的`Wei`，失败时返回`false`，发送2300 gas 的矿工费用，补课调节
    - `<address>.call(...) returns (bool)`：发出调用`<address>`的底层`call`，失败返回`false`，发送所有可用gas，不可调节
    - `<address>.callcode(...) returns (bool)`：发出调用`<address>`的底层`callcode`，失败返回`false`，发送所有可用gas，不可调节
    - `<address>.delegatecall(...) returns (bool)`：发出调用`<address>`的底层`delegatecall`，失败返回`false`，发送所有可用gas，不可调节

- 三种转账方式

    - `<address>.transfer(uint256 amount)`：会引起连锁回滚
    - `<address>.send(utin256 amount) returns (bool)`：不会引起连锁回滚
    - `<address>.call.value(uint256 amount)()`，后面一个括号是执行的代码，不会引起连锁回滚

&emsp;现在回到前面最简单的例子，拍卖例子来看，下图为补充的函数。

![图3-22 bid函数与auctionEnd函数](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306282350329.png)

值得注意的是，下图中，左边为拍卖者的合约账户代码，右图为上图的右图，红色框用的转账是连锁型，所以如果拍卖者的合约账户写的不好，没有`fullback()`的话，那么所有拍卖者以及售卖者都收不到钱，都变成死钱。区块链不可篡改性导致，`Code is law`，也就是bug也不可更改。（注：拍卖者指的是买拍卖品的人，售卖者指的是卖拍卖品的人）

![图3-23 上述代码存在的问题](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290005220.png)

**问**：为什么没有出现root用户，也就是root用户可以更改区块链。答：这样与去中心化相违背，root用户有很大的权力。

下图为上述的改进版本。

![图2-24 改进版本1](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290008981.png)

上图均为售卖者的合约账户，上述仍然存在问题，如果有黑客写下列程序，会有重入攻击。合约账户收到ETH但未调用函数，会执行`fallback()`

![图2-25 黑客的代码](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290012479.png)

上图中`fullback()`会与上上图中`withdraw()`递归调用，导致一直取钱。会有三种情况结束：合约账户没钱了，汽油费没有了，栈stack溢出。所以，下图是修改版本。

![图3-26 修改](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290018699.png)

## The DAO

&emsp;DAO： Decentralized Autonomous Organization，去中心化的自主组织，通用概念。The DAO仅是一个DAO类型的组织，是一个运行在以太坊上的智能合约，通过这个智能合约，达到去中心化的思想，组织的规章制度是写在智能合约中的。DAC： Decentralized Autonomous Corporation，去中心化的自主公司。The DAO只存活了三个月，可以由以太坊来获得代理。The DAO 通过split DAO来使用户取回投资，拆分完之后得到childDAO。前面说过，先清零再转账，出错了再回账，这才是正确处理逻辑。但是，The DAO代码实现中的逻辑问题，导致黑客重入攻击损失5000万美金。

&emsp;The DAO是体量非常大的组织，too big to fail，所以以太坊的开发者们害怕出现太大问题，想要补救，也就是回滚。但是也有很大一部分人不赞同补救，代码就是规则，随意篡改的话会导致去中心化思想遭受挑战。以太坊开发者决定两步走补救，第一步，先发布一个新版本，与The DAO相关账户不作交易，大部分矿工升级了。升级后，有一个bug，与汽油费相关，开发者决定与The DAO相关的地址不收取汽油费。这个导致很多矿工遭受攻击，于是乎大部分矿工又回滚代码，软分叉失败！然后，开发者强行采取强硬的方法，开了一个新的智能合约用来退钱，强行将凡是The DAO账户的钱转到该账户（不验证私钥）。大部分矿工不认可这个方案，所以导致硬分叉。再然后，以太坊开发团队搞了个去中心化投票，最后硬分叉成功了。由于硬分叉，旧链的叫做ETC(Ethereum Classic)，新链的叫ETH，分成了两个以太坊，之后使用chain ID来区分两条链，最后两条链是成为真正意义上的两种币。

## 思考

Smart contract is anything but smart，智能合约不可修改，它是一个代码合同。

Nothing is irrevocable，没有什么是不可篡改的。

Solidity programming language 设计是否有问题，是否应该采用函数式编程。

Ocaml：一种函数式编程语言。

formal verification：形式验证，在形式上验证不可篡改性。

Many eyeball fallacy. 众眼识误。开源软件不一定比不开源软件安全，大众软件不一定比小众软件安全。

去中心化。分叉恰恰是去中心化的体现。

decentralized ≠ distributed，去中心化一定是分布式，分布式不一定是去中心化。去中心化是多台机器重复做同一件事，并且相互验证。分布式是多台机器共同做同一件事情，并且相互组合。去中心化是mission critical application（关键任务应用程序），多台机器共同维护一个状态，比如air traffic control、stock exchange、space shuttle。

智能合约是用来编写控制逻辑，编写共识的，而不是做大量计算的。

暴雪公司把v神的一个角色去除了，所以v神决定创建去中心化的东西。

## 美链

&emsp;美链（Beauty Chain）一个部署在以太坊上的智能合约，有自己的代币BEC，没有自己的区块链，代币的发行、转账都是通过调用智能合约中的函数完成。没有定义自己的发行规则，每个账户有多少代币也是保存在智能合约的状态变量里。ERC（Ethereum Request for Comments） 20是以太坊上发行代币的一个标准，规范了所有发行代币的合约应该实现的功能和遵循的接口。美链中有一个叫batchTransfer的函数（见下图），它的功能是向多个接收者发送代币，然后把这些代币从调用者的账户上扣除。

![图3-27 bathcTransfer实现](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290159531.png)

漏洞之处：`uint256 amount = uint256(cnt) * _value;`会导致溢出，但是后面又用`_value`，所以会导致发行大量的代币。下图是攻击细节。

![图2-28 攻击细节](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290205076.png)

![图2-28 攻击细节](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202306290204013.png)

&emsp;预防措施：使用`SafeMath`库，就可以很容易检测出来。

```javascript
library SafeMath{
    function mul(uint256 a, uint256 b) internal pure returns (uint256 c){
        if(a == 0){
            return 0;
        }
        c = a * b;
        assert(c / a == b);
        return c;
    }
}
```
