# IPv4

`IPv4`是由32位正整数组成，c++中可以使用`uint32_t`来存储，常常使用字符串来表示`xxx.xxx.xxx.xxx`，每一个`xxx`可以为`0-255`。



# CIDR

`IPv4`本来分为A,B,C,D,E五类地址（`x`表示二进制位，`X`表示十进制位）

-   A：`0xxxxxxx.XXX.XXX.XXX`
-   B：`10xxxxxx.XXX.XXX.XXX`
-   C：`110xxxxx.XXX.XXX.XXX`
-   D：`1110xxxx.XXX.XXX.XXX`
-   E：`1111xxxx.XXX.XXX.XXX`

`CIDR`允许使用子网掩码来保证实现自适应的分类方式，不仅仅限于ABCDE五类

那么，`IPv4`地址就由两个`uint32_t`表示，一个是`IP`，一个是`Mask`（子网掩码），常常使用字符串`xxx.xxx.xxx.xxx/x`，每一个`xxx`可为`0-255`，最后`/x`可以为`/0-/32`。



更多划分方式参考[雨探青鸟](https://lmzyoyo.top/archives/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%E6%8A%80%E6%9C%AF%E4%B9%8B%E5%AD%90%E7%BD%91%E5%88%86%E9%85%8D)，定长子网可以使用全0全1作为子网号，非定长则不能使用。





# 特殊IP地址

1.   `0.0.0.0`：可以是全部网络的代称，也可以是本机网络，由于`DHCP`之前没有分配地址，就用这个
2.   `255.255.255.255`：广播地址，对广播域进行广播
3.   `127.x.x.x`：保留，最常用的是`127.0.0.1`本地回环地址
4.   `224.0.0.0-239.255.255.255`：组播地址
5.   `169.254.x.x`：故障地址，一般是DHCP中失败的地址
6.   `10.x.x.x; 172.16.0.0-172.31.255.254; 192.168.x.x`：私有地址



# 相关协议

## DNS

域名解析协议（Domain Name System），根域名服务器，顶级域名服务器，权威域名服务器，….

![域名解析的工作流程](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250303180603284.png)

## ARP/RARP

地址解析协议（Address Resolution Protocol），通过IP地址找MAC地址的协议



逆地址解析协议（Reverse Address Resolution Protocol，RARP），通过MAC地址找IP地址的协议

## DHCP

动态主机配置协议（Dynamic Host Configuration Protocol），底层使用`UDP`

- 服务器监听udp:67
- 客户发送 DHCP发现（DHCPDISCOVER） 报文，目的 255.255.255.255，源 0.0.0.0
- 所有的DHCP服务器都回应 DHCP提供（DHCPOFFER） 报文，目的 255.255.255.255，源 dhcp服务器地址
- 客户选择一个DHCP服务器，并向该服务器发送 DHCP请求报文（DHCPREQUEST），目的 255.255.255.255，源 0.0.0.0，并且数据中携带选择的IP地址
- dhcp-serve发送 DHCPACK ，客户可以用上一步的临时IP了，已绑定状态，数据中携带了租用期 T
- 租用期过了一半，客户重新发送 DHCPREQUEST
- 服务器不同意，发送 DHCPNACK ，客户需要重新申请IP，放弃使用该地址
- 服务器同意，发送 DHCPACK，继续更新了租用期
- 如果服务器没发送，客户需要在 0.875T 时再次发送 DHCPREQUEST
- 客户需要提前终止，只需要发送 DHCPRELEASE 即可


![DHCP 工作流程](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250303180843898.png)

## NAT

网络地址转换协议（Network Address Translation）是内网与外网通信的一种方式，其有四种类型分别是：NAT1、NAT2、NAT3、NAT4。从 NAT1 至 NAT4 限制越来越多。下面分别讲解。

-   NAT1: Full Cone NAT，全锥形NAT，这是最宽松的网络环境，你想做什么，基本没啥限制IP和端口都不受限。
-   NAT2: Address-Restricted Cone NAT，受限锥型NAT，相比NAT1，NAT2 增加了地址限制，也就是IP受限，而端口不受限。
-   NAT3: Port-Restricted Cone NAT，端口受限锥型，相比NAT2，NAT3 又增加了端口限制，也就是说IP、端口都受限。
-   NAT4: Symmetric NAT，对称型NAT，对称型NAT具有端口受限锥型的受限特性，内部地址每一次请求一个特定的外部地址，都可能会绑定到一个新的端口号。也就是请求不同的外部地址映射的端口号是可能不同的。这种类型基本上就告别 P2P 了。

## ICMP

网际控制报文协议（Internet Control Message Protocol）分为`差错报文`和`询问报文`两种

![常见的 ICMP 类型](https://imagere.oss-cn-beijing.aliyuncs.com/PC_PicGO/20250303181344329.png)

-   ping：使用ICMP询问报文
-   traceroute：依次发送TTL为0的三个数据报，然后依次增加TTL再发送三个数据报，重复。因为目的主机IP数据报无法交付UDP，所以回送ICMP终点不可达报文
    

## IGMP

网际组管理协议（Internet Group Management Protocol），用于组播的

-   多播IP转多播MAC：

    >   多播IP：D类地址，1110开始，接下来5位不使用，后面剩下23位，32位
    >
    >   多播MAC：01 00 5E不变，0 + 上面的23位，48位

