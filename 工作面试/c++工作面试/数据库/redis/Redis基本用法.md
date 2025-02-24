# Redis中文网

可以从`Redis`[中文网](https://www.redis.net.cn/tutorial/3501.html)查看相应教程

# 键值对（key）

`Redis`的存储单元就是一个键值对，这一部分说的是键（`key`）的用法，数值类型指的是值，包括字符串，哈希，链表等等

`Redis`的键值对就是

```shell
SET <key> <value> # 设置一个键值对	
GET <key> # 通过键来获取一个值
```

初次之外，还有以下命令

```shell
DEL <key> # 删除一个键，成功返回 (integer) 1，失败返回 (integer) 0
DUMP <key> # 序列化给定的键，并返回序列化的值
```

| 序号 | 小写命令    | 命令及描述                                                   |
| :--: | ----------- | :----------------------------------------------------------- |
|  1   | `del`       | [DEL key](https://www.redis.net.cn/order/3528.html) 该命令用于在 key 存在是删除 key。成功返回`(integer) 1`，失败返回`(integer) 0` |
|  2   | `dump`      | [DUMP key](https://www.redis.net.cn/order/3529.html) 序列化给定 key ，并返回被序列化的值。 |
|  3   | `exists`    | [EXISTS key](https://www.redis.net.cn/order/3530.html) 检查给定 key 是否存在。 |
|  4   | `expire`    | [EXPIRE key seconds](https://www.redis.net.cn/order/3531.html) 为给定 key 设置过期时间。 |
|  5   | `expireat`  | [EXPIREAT key timestamp](https://www.redis.net.cn/order/3532.html) EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。 |
|  6   | `pexpire`   | [PEXPIRE key milliseconds](https://www.redis.net.cn/order/3533.html) 设置 key 的过期时间亿以毫秒计。 |
|  7   | `pexpireat` | [PEXPIREAT key milliseconds-timestamp](https://www.redis.net.cn/order/3534.html) 设置 key 过期时间的时间戳(unix timestamp) 以毫秒计 |
|  8   | `keys`      | [KEYS pattern](https://www.redis.net.cn/order/3535.html) 查找所有符合给定模式( pattern)的 key 。比如`KEYS *`命令，查看所有键 |
|  9   | `move`      | [MOVE key db](https://www.redis.net.cn/order/3536.html) 将当前数据库的 key 移动到给定的数据库 db 当中，默认是数据库`0`，可以使用`SELECT`命令来切换数据库，比如`SELECT 1` |
|  10  | `persist`   | [PERSIST key](https://www.redis.net.cn/order/3537.html) 移除 key 的过期时间，key 将持久保持。 |
|  11  | `pttl`      | [PTTL key](https://www.redis.net.cn/order/3538.html) 以毫秒为单位返回 key 的剩余的过期时间。 |
|  12  | `ttl`       | [TTL key](https://www.redis.net.cn/order/3539.html) 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)，`-1`表示永不过期，`-2`表示已经过期 |
|  13  | `randomkey` | [RANDOMKEY](https://www.redis.net.cn/order/3540.html) 从当前数据库中随机返回一个 key 。 |
|  14  | `rename`    | [RENAME Key NewKey](https://www.redis.net.cn/order/3541.html) 修改 key 的名称 |
|  15  | `renamenx`  | [RENAMENX Key NewKey](https://www.redis.net.cn/order/3542.html) 仅当 `NewKey`不存在时，将 `Key`改名为 `NewKey`。 |
|  16  | `type`      | [TYPE key](https://www.redis.net.cn/order/3543.html) 返回 key 所储存的值的类型。 |

# 字符串（String）

从这里开始，讲的就是`Redis`存储单元——键值对，中的值了，该内容值得是字符串值

默认字符串写中文的话，会显示十六进制，如果需要显示原始数据，那么就需要在执行`redis-cli`的时候加上参数`–-raw`，即`redis-cli --raw`

| 序号 | 小写命令      | 命令及描述                                                   |
| :--: | ------------- | :----------------------------------------------------------- |
|  1   | `set`         | [SET key value](https://www.redis.net.cn/order/3544.html) 设置指定 key 的值 |
|  2   | `get`         | [GET key](https://www.redis.net.cn/order/3545.html) 获取指定 key 的值。 |
|  3   | `getrange`    | [GETRANGE key start end](https://www.redis.net.cn/order/3546.html) 返回 key 中字符串值的子字符，区间是`[start, end]`，都是闭区间，负数表示倒数第几个成员，参考python |
|  4   | `getset`      | [GETSET key value](https://www.redis.net.cn/order/3547.html) 将给定 key 的值设为 value ，并返回 key 的旧值(old value)。 |
|  5   | `getbit`      | [GETBIT key offset](https://www.redis.net.cn/order/3548.html) 对 key 所储存的字符串值，获取指定偏移量上的位(bit)。 |
|  6   | `mget`        | [MGET key1 key2…](https://www.redis.net.cn/order/3549.html) 获取所有(一个或多个)给定 key 的值。 |
|  7   | `setbit`      | [SETBIT key offset value](https://www.redis.net.cn/order/3550.html) 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。 |
|  8   | `setex`       | [SETEX key seconds value](https://www.redis.net.cn/order/3551.html) 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。 |
|  9   | `setnx`       | [SETNX key value](https://www.redis.net.cn/order/3552.html) 只有在 key 不存在时设置 key 的值。 |
|  10  | `setrange`    | [SETRANGE key offset value](https://www.redis.net.cn/order/3553.html) 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。 |
|  11  | `strlen`      | [STRLEN key](https://www.redis.net.cn/order/3554.html) 返回 key 所储存的字符串值的长度。 |
|  12  | `mset`        | [MSET key1 value1 key2 value2 ...](https://www.redis.net.cn/order/3555.html) 同时设置一个或多个 key-value 对。 |
|  13  | `msetnx`      | [MSETNX key1 value1 key2 value2 ...](https://www.redis.net.cn/order/3556.html) 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。 |
|  14  | `psetex`      | [PSETEX key milliseconds value](https://www.redis.net.cn/order/3557.html) 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。 |
|  15  | `incr`        | [INCR key](https://www.redis.net.cn/order/3558.html) 将 key 中储存的数字值增一。 |
|  16  | `incrby`      | [INCRBY key increment](https://www.redis.net.cn/order/3559.html) 将 key 所储存的值加上给定的增量值（increment） 。 |
|  17  | `incrbyfloat` | [INCRBYFLOAT key increment](https://www.redis.net.cn/order/3560.html) 将 key 所储存的值加上给定的浮点增量值（increment） 。 |
|  18  | `decr`        | [DECR key](https://www.redis.net.cn/order/3561.html) 将 key 中储存的数字值减一。 |
|  19  | `decrby`      | [DECRBY key decrement](https://www.redis.net.cn/order/3562.html) key 所储存的值减去给定的减量值（decrement） 。 |
|  20  | `append`      | [APPEND key value](https://www.redis.net.cn/order/3563.html) 如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。 |

# 哈希（Hash）

哈希，哈希存储的是`field, value`对，不妨叫做阈值对，阈值对的集合作为一个哈希结构的`Value`，同样，哈希结构和字符串一样，是有`key`的，因为哈希结构只是键值对中的值。

| 序号 | 小写命令       | 命令及描述                                                   |
| :--- | -------------- | :----------------------------------------------------------- |
| 1    | `hdel`         | [HDEL key field2 field2…](https://www.redis.net.cn/order/3564.html) 删除一个或多个哈希表字段 |
| 2    | `hexists`      | [HEXISTS key field](https://www.redis.net.cn/order/3565.html) 查看哈希表 key 中，指定的字段是否存在。 |
| 3    | `hget`         | [HGET key field](https://www.redis.net.cn/order/3566.html) 获取存储在哈希表中指定字段的值/td> |
| 4    | `hgetall`      | [HGETALL key](https://www.redis.net.cn/order/3567.html) 获取在哈希表中指定 key 的所有字段和值 |
| 5    | `hincrby`      | [HINCRBY key field increment](https://www.redis.net.cn/order/3568.html) 为哈希表 key 中的指定字段的整数值加上增量 increment 。 |
| 6    | `hincrbyfloat` | [HINCRBYFLOAT key field increment](https://www.redis.net.cn/order/3569.html) 为哈希表 key 中的指定字段的浮点数值加上增量 increment 。 |
| 7    | `hkeys`        | [HKEYS key](https://www.redis.net.cn/order/3570.html) 获取所有哈希表中的字段 |
| 8    | `hlen`         | [HLEN key](https://www.redis.net.cn/order/3571.html) 获取哈希表中字段的数量 |
| 9    | `hmget`        | [HMGET key field1 field2](https://www.redis.net.cn/order/3572.html) 获取所有给定字段的值 |
| 10   | `hmset`        | [HMSET key field1 value1 field2 value2 …](https://www.redis.net.cn/order/3573.html) 同时将多个 field-value (域-值)对设置到哈希表 key 中。 |
| 11   | `hset`         | [HSET key field value](https://www.redis.net.cn/order/3574.html) 将哈希表 key 中的字段 field 的值设为 value 。 |
| 12   | `hsetnx`       | [HSETNX key field value](https://www.redis.net.cn/order/3575.html) 只有在字段 field 不存在时，设置哈希表字段的值。 |
| 13   | `hvals`        | [HVALS key](https://www.redis.net.cn/order/3576.html) 获取哈希表中所有值 |
| 14   | `hscan`        | HSCAN key cursor [MATCH pattern] [COUNT count] 迭代哈希表中的键值对，其中`pattern`和`count`从字面意思就知道是匹配`键`串和数量，`cursor`是游标，可以认为是迭代器（或下标），每次调用`hscan`命令会返回一个新的`cursor`，下次则需要使用这个新的`cursor`。`pattern`类似于字符串命令中的`keys`，用于匹配键。 |

# 列表（List）

列表存储着字符串列表，可以在头（左）或者尾（右）添加元素

| 序号 | 小写命令      | 命令及描述                                                   |
| :--- | ------------- | :----------------------------------------------------------- |
| 1    | `blpop`       | [BLPOP key1 key2  timeout](https://www.redis.net.cn/order/3577.html) 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 2    | `brpop`       | [BRPOP key1 key2  timeout](https://www.redis.net.cn/order/3578.html) 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 3    | `brpopplpush` | [BRPOPLPUSH source destination timeout](https://www.redis.net.cn/order/3579.html) 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 4    | `lindex`      | [LINDEX key index](https://www.redis.net.cn/order/3580.html) 通过索引获取列表中的元素 |
| 5    | `linsert`     | [LINSERT key BEFORE\|AFTER pivot value](https://www.redis.net.cn/order/3581.html) 在列表的元素前或者后插入元素 |
| 6    | `llen`        | [LLEN key](https://www.redis.net.cn/order/3582.html) 获取列表长度 |
| 7    | `lpop`        | [LPOP key](https://www.redis.net.cn/order/3583.html) 移出并获取列表的第一个元素 |
| 8    | `lpush`       | [LPUSH key value1 value2](https://www.redis.net.cn/order/3584.html) 将一个或多个值插入到列表头部，注意插入的顺序是从`value1`到`value2`到`...` |
| 9    | `lpushx`      | [LPUSHX key value1  value2](https://www.redis.net.cn/order/3585.html) 将一个或多个值插入到已存在的列表头部，与上面不同之处在于，该命令在列表不存在时无效，上述命令在列表不存在时创建列表 |
| 10   | `lrange`      | [LRANGE key start stop](https://www.redis.net.cn/order/3586.html) 获取列表指定范围内的元素 |
| 11   | `lrem`        | [LREM key count value](https://www.redis.net.cn/order/3587.html) 移除列表元素 |
| 12   | `lset`        | [LSET key index value](https://www.redis.net.cn/order/3588.html) 通过索引设置列表元素的值 |
| 13   | `ltrim`       | [LTRIM key start stop](https://www.redis.net.cn/order/3589.html) 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。 |
| 14   | `rpop`        | [RPOP key](https://www.redis.net.cn/order/3590.html) 移除并获取列表最后一个元素 |
| 15   | `rpoplpush`   | [RPOPLPUSH source destination](https://www.redis.net.cn/order/3591.html) 移除列表的最后一个元素，并将该元素添加到另一个列表并返回 |
| 16   | `rpush`       | [RPUSH key value1 value2](https://www.redis.net.cn/order/3592.html) 在列表中添加一个或多个值 |
| 17   | `rpushx`      | [RPUSHX key value1 value2](https://www.redis.net.cn/order/3593.html) 为已存在的列表添加值，可参考`lpushx` |

# 集合（Set）

集合采用哈希存储，并且集合内元素将会唯一

| 序号 | 小写命令      | 命令及描述                                                   |
| :--- | ------------- | :----------------------------------------------------------- |
| 1    | `sadd`        | [SADD key member1 member2](https://www.redis.net.cn/order/3594.html) 向集合添加一个或多个成员 |
| 2    | `scard`       | [SCARD key](https://www.redis.net.cn/order/3595.html) 获取集合的成员数 |
| 3    | `sdiff`       | [SDIFF key1 key2](https://www.redis.net.cn/order/3596.html) 返回给定两个集合的差集，也可以`key2`不指定，那么就为空集合 |
| 4    | `sdiffstore`  | [SDIFFSTORE destination key1 key2](https://www.redis.net.cn/order/3597.html) 返回给定`key1`集合和`key2`集合的差集并存储在 `destination `中 |
| 5    | `sinter`      | [SINTER key1 key2](https://www.redis.net.cn/order/3598.html) 返回给定所有集合的交集 |
| 6    | `sinterstore` | [SINTERSTORE destination key1 key2](https://www.redis.net.cn/order/3599.html) 返回给定所有集合的交集并存储在 destination 中，参考上面 |
| 7    | `sismember`   | [SISMEMBER key member](https://www.redis.net.cn/order/3600.html) 判断 member 元素是否是集合 key 的成员 |
| 8    | `smembers`    | [SMEMBERS key](https://www.redis.net.cn/order/3601.html) 返回集合中的所有成员 |
| 9    | `smove`       | [SMOVE source destination member](https://www.redis.net.cn/order/3602.html) 将 member 元素从 source 集合移动到 destination 集合 |
| 10   | `spop`        | [SPOP key](https://www.redis.net.cn/order/3603.html) 移除并返回集合中的一个随机元素 |
| 11   | `srandmember` | [SRANDMEMBER key count](https://www.redis.net.cn/order/3604.html) 返回集合中一个或多个随机数 |
| 12   | `srem`        | [SREM key member1 member2](https://www.redis.net.cn/order/3605.html) 移除集合中一个或多个成员 |
| 13   | `sunion`      | [SUNION key1 key2](https://www.redis.net.cn/order/3606.html) 返回所有给定集合的并集 |
| 14   | `sunionstore` | [SUNIONSTORE destination key1 key2](https://www.redis.net.cn/order/3607.html) 所有给定集合的并集存储在 destination 集合中 |
| 15   | `sscan`       | [SSCAN key cursor [MATCH pattern\] [COUNT count]](https://www.redis.net.cn/order/3608.html) 迭代集合中的元素，可参考哈希中的`hscan` |

# 有序集合（zset）

有序集合（sorted set）仍然存储着不重复的string元素，但是会为每个元素设定一个double类型的分数，这个分数就是排序的依据，从小到大排序，分数可重复，元素需唯一。

| 序号 | 小写命令           | 命令及描述                                                   |
| :--- | ------------------ | :----------------------------------------------------------- |
| 1    | `zadd`             | [ZADD key score1 member1 score2 member2](https://www.redis.net.cn/order/3609.html) 向有序集合添加一个或多个成员，或者更新已存在成员的分数 |
| 2    | `zcard`            | [ZCARD key](https://www.redis.net.cn/order/3610.html) 获取有序集合的成员数 |
| 3    | `zcount`           | [ZCOUNT key min max](https://www.redis.net.cn/order/3611.html) 计算在有序集合中指定区间分数的成员数 |
| 4    | `zincrby`          | [ZINCRBY key increment member](https://www.redis.net.cn/order/3612.html) 有序集合中对指定成员的分数加上增量 increment |
| 5    | `zinterstore`      | [ZINTERSTORE destination numkeys key1 key2 ...](https://www.redis.net.cn/order/3613.html) 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中 |
| 6    | `zlexcount`        | [ZLEXCOUNT key min max](https://www.redis.net.cn/order/3614.html) 在有序集合中计算指定字典区间内成员数量，`[min, max]`区间，左右都是闭区间 |
| 7    | `zrange`           | [ZRANGE key start stop WITHSCORES](https://www.redis.net.cn/order/3615.html) 通过索引区间返回有序集合成指定区间内的成员，返回顺序时递增的，以及元素的字典序，负数表示倒数第几个成员，返回区间是`[start, stop]`闭区间，`WITHSCORES`表示分数也返回（没有则表示不返回分数） |
| 8    | `zrangebylex`      | [ZRANGEBYLEX key min max LIMIT offset count](https://www.redis.net.cn/order/3616.html) 通过字典区间返回有序集合的成员 |
| 9    | `zrangebyscore`    | [ZRANGEBYSCORE key min max [WITHSCORES\] [LIMIT]](https://www.redis.net.cn/order/3617.html) 通过分数返回有序集合指定区间内的成员 |
| 10   | `zrank`            | [ZRANK key member](https://www.redis.net.cn/order/3618.html) 返回有序集合中指定成员的索引 |
| 11   | `zrem`             | [ZREM key member member ...](https://www.redis.net.cn/order/3619.html) 移除有序集合中的一个或多个成员 |
| 12   | `zremrangebylex`   | [ZREMRANGEBYLEX key min max](https://www.redis.net.cn/order/3620.html) 移除有序集合中给定的字典区间的所有成员 |
| 13   | `zremrangebyrank`  | [ZREMRANGEBYRANK key start stop](https://www.redis.net.cn/order/3621.html) 移除有序集合中给定的排名区间的所有成员 |
| 14   | `zremrangebyscore` | [ZREMRANGEBYSCORE key min max](https://www.redis.net.cn/order/3622.html) 移除有序集合中给定的分数区间的所有成员 |
| 15   | `zrevrange`        | [ZREVRANGE key start stop WITHSCORES](https://www.redis.net.cn/order/3623.html) 返回有序集中指定区间内的成员，通过索引，分数从高到底 |
| 16   | `zrevrangebyscore` | [ZREVRANGEBYSCORE key max min WITHSCORES](https://www.redis.net.cn/order/3624.html) 返回有序集中指定分数区间内的成员，分数从高到低排序 |
| 17   | `zrevrank`         | [ZREVRANK key member](https://www.redis.net.cn/order/3625.html) 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
| 18   | `zscore`           | [ZSCORE key member](https://www.redis.net.cn/order/3626.html) 返回有序集中，成员的分数值 |
| 19   | `zunionstore`      | [ZUNIONSTORE destination numkeys key key ...](https://www.redis.net.cn/order/3627.html) 计算给定的一个或多个有序集的并集，并存储在新的 key 中 |
| 20   | `zscan`            | [ZSCAN key cursor [MATCH pattern\] [COUNT count]](https://www.redis.net.cn/order/3628.html) 迭代有序集合中的元素（包括元素成员和元素分值） |

# 基数（HyperLogLog）

一个集合的基数集合就是对这个集合去重之后的集合，基数表示基数集合的元素。`Redis`中的基数不会保存元素，只会保存基数，但是同样会对元素进行去重。因此，基数可以用很小的存储来输出很大的基数集合的基数。

| 序号 | 小写命令  | 命令及描述                                                   |
| :--- | --------- | :----------------------------------------------------------- |
| 1    | `pfadd`   | [PFADD key element1 element2 ...](https://www.redis.net.cn/order/3629.html) 添加指定元素到 HyperLogLog 中。 |
| 2    | `pfcount` | [PFCOUNT key1 key2 ...](https://www.redis.net.cn/order/3630.html) 返回给定 HyperLogLog 的基数估算值，将`key1, key2...`看作一个集合 |
| 3    | `pfmerge` | [PFMERGE destkey sourcekey1 sourcekey2...](https://www.redis.net.cn/order/3631.html) 将多个 HyperLogLog 合并为一个 HyperLogLog |

# 发布/订阅

发送者（pub）发送消息，订阅者（sub）接收消息，客户端可以订阅任意数量的频道（channel）。当有消息发送给频道时，这个消息会被发送给所有订阅它的客户端。

| 序号 | 小写命令       | 命令及描述                                                   |
| :--- | -------------- | :----------------------------------------------------------- |
| 1    | `psubscribe`   | [PSUBSCRIBE pattern1 pattern2 ...](https://www.redis.net.cn/order/3632.html) 订阅一个或多个符合给定模式的频道。 |
| 2    | `pubsub`       | [PUBSUB subcommand [argument [argument ...\]]](https://www.redis.net.cn/order/3633.html) 查看订阅与发布系统状态。 |
| 3    | `publish`      | [PUBLISH channel message](https://www.redis.net.cn/order/3634.html) 将信息发送到指定的频道。 |
| 4    | `punsubscribe` | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.redis.net.cn/order/3635.html) 退订所有给定模式的频道。 |
| 5    | `subscribe`    | [SUBSCRIBE channel1 channel2 ...](https://www.redis.net.cn/order/3636.html) 订阅给定的一个或多个频道的信息。 |
| 6    | `unsubscribe`  | [UNSUBSCRIBE [channel [channel ...\]]](https://www.redis.net.cn/order/3637.html) 指退订给定的频道。 |

# 事务

单条`Redis`命令虽然是满足原子性的，但是事务是为了多条`Redis`命令服务的，要求多条`Redis`命令也要满足原子性。

| 序号 | 小写命令  | 命令及描述                                                   |
| :--- | --------- | :----------------------------------------------------------- |
| 1    | `discard` | [DISCARD](https://www.redis.net.cn/order/3638.html) 取消事务，放弃执行事务块内的所有命令。 |
| 2    | `exec`    | [EXEC](https://www.redis.net.cn/order/3639.html) 执行所有事务块内的命令。 |
| 3    | `multi`   | [MULTI](https://www.redis.net.cn/order/3640.html) 标记一个事务块的开始。 |
| 4    | `unwatch` | [UNWATCH](https://www.redis.net.cn/order/3641.html) 取消 WATCH 命令对所有 key 的监视。 |
| 5    | `watch`   | [WATCH key1 key2 ...](https://www.redis.net.cn/order/3642.html) 监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断 |

# 脚本

`Redis`使用`Lua`脚本来执行，内嵌了`Lua`解释器，执行脚本命令为`EVAL`

| 序号 | 小写命令        | 命令及描述                                                   |
| :--- | --------------- | :----------------------------------------------------------- |
| 1    | `eval`          | [EVAL script numkeys key [key ...\] arg [arg ...]](https://www.redis.net.cn/order/3643.html) 执行 Lua 脚本，`numkeys`表示`key`的个数，传入的`key`，`lua`语言可以使用`KEYS[]`数组访问，下标从1开始。传入的`arg`，`lua`语言可以使用`ARGV[]`数组访问，下标从1开始。 |
| 2    | `evalsha`       | [EVALSHA sha1 numkeys key [key ...\] arg [arg ...]](https://www.redis.net.cn/order/3644.html) 执行 Lua 脚本，同上，不过`sha1`是验证脚本的哈希值 |
| 3    | `script exists` | [SCRIPT EXISTS script script ...](https://www.redis.net.cn/order/3645.html) 查看指定的脚本是否已经被保存在缓存当中。 |
| 4    | `script flush`  | [SCRIPT FLUSH](https://www.redis.net.cn/order/3646.html) 从脚本缓存中移除所有脚本。 |
| 5    | `script kill`   | [SCRIPT KILL](https://www.redis.net.cn/order/3647.html) 杀死当前正在运行的 Lua 脚本。 |
| 6    | `script load`   | [SCRIPT LOAD script](https://www.redis.net.cn/order/3648.html) 将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本，并且会返回`sha1`哈希值。 |

# 客户端一些命令

使用客户端连接到了`Redis`服务中，可以进行以下一些命令

| 序号 | 小写命令 | 命令及描述                                                   |
| :--- | -------- | :----------------------------------------------------------- |
| 1    | `auth`   | [AUTH password](https://www.redis.net.cn/order/3649.html) 验证密码是否正确 |
| 2    | `echo`   | [ECHO message](https://www.redis.net.cn/order/3650.html) 打印字符串 |
| 3    | `ping`   | [PING](https://www.redis.net.cn/order/3651.html) 查看服务是否运行 |
| 4    | `quit`   | [QUIT](https://www.redis.net.cn/order/3652.html) 关闭当前连接 |
| 5    | `select` | [SELECT index](https://www.redis.net.cn/order/3653.html) 切换到指定的数据库 |

# 关于服务器的一些命令

连接到服务器之后，也可以使用下列一些命令管理服务器

| 序号 | 小写命令           | 命令及描述                                                   |
| :--- | ------------------ | :----------------------------------------------------------- |
| 1    | `bgrewriteaof`     | [BGREWRITEAOF](https://www.redis.net.cn/order/3654.html) 异步执行一个 AOF（AppendOnly File） 文件重写操作 |
| 2    | `bgsave`           | [BGSAVE](https://www.redis.net.cn/order/3655.html) 在后台异步保存当前数据库的数据到磁盘 |
| 3    | `client kill`      | [CLIENT KILL [ip:port\] [ID client-id]](https://www.redis.net.cn/order/3656.html) 关闭客户端连接 |
| 4    | `client list`      | [CLIENT LIST](https://www.redis.net.cn/order/3657.html) 获取连接到服务器的客户端连接列表 |
| 5    | `client getname`   | [CLIENT GETNAME](https://www.redis.net.cn/order/3658.html) 获取连接的名称 |
| 6    | `client pause`     | [CLIENT PAUSE timeout](https://www.redis.net.cn/order/3659.html) 在指定时间内终止运行来自客户端的命令 |
| 7    | `client setname`   | [CLIENT SETNAME connection-name](https://www.redis.net.cn/order/3660.html) 设置当前连接的名称 |
| 8    | `cluster slots`    | [CLUSTER SLOTS](https://www.redis.net.cn/order/3661.html) 获取集群节点的映射数组 |
| 9    | `command`          | [COMMAND](https://www.redis.net.cn/order/3662.html) 获取 Redis 命令详情数组 |
| 10   | `command count`    | [COMMAND COUNT](https://www.redis.net.cn/order/3663.html) 获取 Redis 命令总数 |
| 11   | `command getkeys`  | [COMMAND GETKEYS](https://www.redis.net.cn/order/3664.html) 获取给定命令的所有键 |
| 12   | `time`             | [TIME](https://www.redis.net.cn/order/3665.html) 返回当前服务器时间 |
| 13   | `command info`     | [COMMAND INFO command-name1 command-name2 ...](https://www.redis.net.cn/order/3666.html) 获取指定 Redis 命令描述的数组 |
| 14   | `config get`       | [CONFIG GET parameter](https://www.redis.net.cn/order/3667.html) 获取指定配置参数的值 |
| 15   | `config rewrite`   | [CONFIG REWRITE](https://www.redis.net.cn/order/3668.html) 对启动 Redis 服务器时所指定的 redis.conf 配置文件进行改写 |
| 16   | `config set`       | [CONFIG SET parameter value](https://www.redis.net.cn/order/3669.html) 修改 redis 配置参数，无需重启 |
| 17   | `config resetstat` | [CONFIG RESETSTAT](https://www.redis.net.cn/order/3670.html) 重置 INFO 命令中的某些统计数据 |
| 18   | `dbsize`           | [DBSIZE](https://www.redis.net.cn/order/3671.html) 返回当前数据库的 key 的数量 |
| 19   | `debug`            | [DEBUG OBJECT key](https://www.redis.net.cn/order/3672.html) 获取 key 的调试信息 |
| 20   | `debug segfault`   | [DEBUG SEGFAULT](https://www.redis.net.cn/order/3673.html) 让 Redis 服务崩溃 |
| 21   | `flushall`         | [FLUSHALL](https://www.redis.net.cn/order/3674.html) 删除所有数据库的所有key |
| 22   | `flushdb`          | [FLUSHDB](https://www.redis.net.cn/order/3675.html) 删除当前数据库的所有key |
| 23   | `info`             | [INFO section](https://www.redis.net.cn/order/3676.html) 获取 Redis 服务器的各种信息和统计数值 |
| 24   | `lastsave`         | [LASTSAVE](https://www.redis.net.cn/order/3677.html) 返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示 |
| 25   | `monitor`          | [MONITOR](https://www.redis.net.cn/order/3678.html) 实时打印出 Redis 服务器接收到的命令，调试用 |
| 26   | `role`             | [ROLE](https://www.redis.net.cn/order/3679.html) 返回主从实例所属的角色 |
| 27   | `save`             | [SAVE](https://www.redis.net.cn/order/3680.html) 异步保存数据到硬盘 |
| 28   | `shutdown`         | [SHUTDOWN [NOSAVE\] [SAVE]](https://www.redis.net.cn/order/3681.html) 异步保存数据到硬盘，并关闭服务器 |
| 29   | `slaveof`          | [SLAVEOF host port](https://www.redis.net.cn/order/3682.html) 将当前服务器转变为指定服务器的从属服务器(slave server) |
| 30   | `slowlog`          | [SLOWLOG subcommand argument](https://www.redis.net.cn/order/3683.html) 管理 redis 的慢日志 |
| 31   | `sync`             | [SYNC](https://www.redis.net.cn/order/3684.html) 用于复制功能(replication)的内部命令 |

