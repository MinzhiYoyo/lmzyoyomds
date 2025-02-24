# 介绍

Redis（Remote Dictionary Server）是一个开源的内存数据库，使用键值对存储系统，属于非关系型（no SQL）数据库。有如下特点：

-   性能高，支持每面十万次读写操作，适合高并发
-   数据类型丰富：包括键值对，字符串、列表、集合、哈希表、有序集合等
-   原子性：所有的操作都是具有原子性的，天然适合多线程
-   持久化：可以把数据保存在磁盘中
-   支持发布/订阅：内置发布订阅模式，客户端之间通过消息传递同行，适合做消息队列
-   单线程模式：它是单线程的，通过事件驱动模型处理并发请求
-   主从复制：可以通过从节点来备份或者负载均衡
-   支持`Lua`脚本：可以用`Lua`脚本来编写复杂功能

# 安装

```shell
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

sudo apt-get update
sudo apt-get install redis
sudo systemctl start redis-server
sudo systemctl enable redis-server
```

# 配置

```shell
# 进入命令行客户端
redis-cli

# 下面出现就是代表进入了客户端
redis 127.0.0.1:6379> 
```

然后执行基本的配置命令

```shell
CONFIG GET *  # 获取所有配置

CONFIG GET parameter_name [...] # 获取某个配置

CONFIG SET parameter_name parameter_value
```

同样的，也可以在`/etc/redis/redis.conf`文件中查看配置信息

| 序号 | 配置项                                                       | 说明                                                         |
| :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1    | `daemonize no`                                               | Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ） |
| 2    | `pidfile /var/run/redis.pid`                                 | 当 Redis 以守护进程方式运行时，Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定 |
| 3    | `port 6379`                                                  | 指定 Redis 监听端口，默认端口为 6379，作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女 Alessia Merz 的名字 |
| 4    | `bind 127.0.0.1`                                             | 绑定的主机地址                                               |
| 5    | `timeout 300`                                                | 当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能  |
| 6    | `loglevel notice`                                            | 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice |
| 7    | `logfile stdout`                                             | 日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null |
| 8    | `databases 16`                                               | 设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id |
| 9    | `save <seconds> <changes>`Redis 默认配置文件中提供了三个条件：**save 900 1****save 300 10****save 60 10000**分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。 | 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合 |
| 10   | `rdbcompression yes`                                         | 指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大 |
| 11   | `dbfilename dump.rdb`                                        | 指定本地数据库文件名，默认值为 dump.rdb                      |
| 12   | `dir ./`                                                     | 指定本地数据库存放目录                                       |
| 13   | `slaveof <masterip> <masterport>`                            | 设置当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步 |
| 14   | `masterauth <master-password>`                               | 当 master 服务设置了密码保护时，slave 服务连接 master 的密码 |
| 15   | `requirepass foobared`                                       | 设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH <password> 命令提供密码，默认关闭 |
| 16   | ` maxclients 128`                                            | 设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息 |
| 17   | `maxmemory <bytes>`                                          | 指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区 |
| 18   | `appendonly no`                                              | 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no |
| 19   | `appendfilename appendonly.aof`                              | 指定更新日志文件名，默认为 appendonly.aof                    |
| 20   | `appendfsync everysec`                                       | 指定更新日志条件，共有 3 个可选值：**no**：表示等操作系统进行数据缓存同步到磁盘（快）**always**：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）**everysec**：表示每秒同步一次（折中，默认值） |
| 21   | `vm-enabled no`                                              | 指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制） |
| 22   | `vm-swap-file /tmp/redis.swap`                               | 虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享 |
| 23   | `vm-max-memory 0`                                            | 将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0 |
| 24   | `vm-page-size 32`                                            | Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值 |
| 25   | `vm-pages 134217728`                                         | 设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。 |
| 26   | `vm-max-threads 4`                                           | 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4 |
| 27   | `glueoutputbuf yes`                                          | 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启 |
| 28   | `hash-max-zipmap-entries 64 hash-max-zipmap-value 512`       | 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法 |
| 29   | `activerehashing yes`                                        | 指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍） |
| 30   | `include /path/to/local.conf`                                | 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件 |

# 数据类型

-   **string（字符串）:** 基本的数据存储单元，可以存储字符串、整数或者浮点数。并且是二进制安全的，可以存储任何序列化信息，如图片等，最大容量是512MB
-   **hash（哈希）:**一个键值对集合，可以存储多个字段。每个哈希最多存储$2^{32}-1$个键值对
-   **list（列表）:**一个简单的列表，可以存储一系列的字符串元素。按照插入顺序排序，可以在头部（左边）或尾部（右边）添加数据，最多存储$2^{32}-1$个元素
-   **set（集合）:**一个无序集合，可以存储不重复的字符串元素。使用哈希表实现
-   **zset(sorted set：有序集合):** 类似于集合，但是每个元素都有一个分数（score）与之关联，也只能存储不重复的字符串元素。成员唯一，分数可重复
-   **位图（Bitmaps）：**基于字符串类型，可以对每个位进行操作。
-   **超日志（HyperLogLogs）：**用于基数统计，可以估算集合中的唯一元素数量。
-   **地理空间（Geospatial）：**用于存储地理位置信息。
-   **发布/订阅（Pub/Sub）：**一种消息通信模式，允许客户端订阅消息通道，并接收发布到该通道的消息。
-   **流（Streams）：**用于消息队列和日志存储，支持消息的持久化和时间排序。
-   **模块（Modules）：**Redis 支持动态加载模块，可以扩展 Redis 的功能。

# 基本命令

```shell
redis-cli # 登录本机

redis 127.0.0.1:6379> PING # 检查连通性
```

也可以登录远程

```shell
redis-cli -h host -p port -a password
```

登录之后，就会显示ip地址及端口

```shell
redis 127.0.0.1:6379>
```

这里开始，输入的就是`Redis`命令了，注意，`Redis`命令不区分大小写，但是`Key`和`Value`等区分大小写，推荐使用大写作为命令。

redis可以用多个数据库，默认是数据库`0`，可以用`SELECT`命令来选择

```shell
SELECT 0 # 选择第0号数据库
```

# 管道技术

客户端可以一次性将多个字符串发送给服务端，这样服务端执行结束后会一次性返回结果，效率会高很多。

比如将以下字符串一次性发送给服务器是没问题的，注意两条命令之间用`\r\n`区分

```shell
PING\r\n SET testkey redis\r\nGET testkey\r\nINCR visitor\r\nINCR visitor\r\nINCR visitor\r\n
```

# 备份与恢复

```shell
redis 127.0.0.1:6379> SAVE  # 保存数据，会在安装目录生成 dump.rdb，默认是 /usr/local/redis/bin，也可以通过config get dir命令来查看

# bgsave 是在后台执行的而已
```

# 密码

```shell
127.0.0.1:6379> CONFIG get requirepass  # 查看是否有密码验证

config set requirepass "【redis访问密码】" # 设置密码才能使用

# 如果使用 redis-cli 登录，是需要手动验证密码的
auth "【redis访问密码】"  # 用来验证密码，否则其他东西用不了的
```

# 性能测试

不登陆，直接在`shell`窗口使用`redis-benchmark`命令实现

```shell
redis-benchmark [选项] [选项值]
```

| 序号 | 选项      | 描述                                       | 默认值    |
| :--- | :-------- | :----------------------------------------- | :-------- |
| 1    | **-h**    | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**    | 指定服务器端口                             | 6379      |
| 3    | **-s**    | 指定服务器 socket                          |           |
| 4    | **-c**    | 指定并发连接数                             | 50        |
| 5    | **-n**    | 指定请求数                                 | 10000     |
| 6    | **-d**    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**    | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**    | 通过管道传输 `<numreq>` 请求               | 1         |
| 10   | **-q**    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv** | 以 CSV 格式输出                            |           |
| 12   | **-l**    | 生成循环，永久执行测试                     |           |
| 13   | **-t**    | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | **-I**    | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

