# 持久化方法

主要包括`Append Only File(AOF)`和`RDB`快照方法，前者是把命令写入磁盘，后者是把数据写入磁盘。

# AOF

## 基本介绍

AOF的基本功能就是，每次执行客户端命令时，同样会记录该命令。只记录`写`操作的命令，不记录`读`操作的命令。

默认没开启，可以在`redis.conf`开启：

```yaml
appendonlye yes
appendfilename "appendonly.aof"
```

命令结构如下，以`set name redis`为例：

```shell
*3
$3
set
$4
name
$5
redis
```

-   `*n`：表示命令由`n`个部分组成，比如`*3`
-   `$n`：表示当前部分有`n`个字节，比如`$3,$4,$5`

顺序为先执行操作，然后再写入命令，优缺点如下：

-   优点：不需要额外检查命令
-   优点：不会阻塞客户端
-   缺点：有丢失风险，如果先写入了，然后宕机了就会记录失败
-   缺点：对下一个命令有阻塞可能

## 写入策略

AOF会先写到内存的缓冲区中，然后再统一写入磁盘，这样会更加快，有三种策列，可以通过设置`redis.conf`中的`appendfsync`

-   `Always`：执行一条命令就写入一条，执行`fsync()`函数
-   `Everysec`：每个一秒钟就写入一次，每隔一秒执行一次`fsync()`函数
-   `No`：只写到内核缓冲区，由操作系统决定何时写入磁盘，从不执行`fsync()`函数

## AOF重写

一个AOF文件会变得越来越大的，那么太大的文件就会影响性能。由于对于同一个变量进行多次写入操作，终究会有个最终的结果，那么其实只要写入这个最终结果即可，因此可以对AOF文件重写。



AOF重写是在后台**子进程**(`bgwriteaof`)完成的，主进程`fork`出子进程，不会阻塞主进程的接口。由于`fork`出的父子线程内存是一致的，操作系统会复制父进程的所有内存给子进程，但是物理内存不会复制。只有当父子进程对内存进行写时，才会复制对应的物理内存，这称之为`写时复制`。



父进程（接口进程）和子进程（日志进程）会各自执行各自的职责：

-   父进程继续服务客户端，如果发生了数据更改，那么操作系统会进行写时复制，子进程获取的数据只会有`fork`时的数据版本。
-   父进程此时不仅需要将命令写入AOF缓冲区，还需要把命令写入AOF重写缓冲区，这是因为父子进程的数据版本已经不一致了。
-   子进程对文件进行AOF重写操作（为每一条数据生成一条命令），并写入到一个新的文件中
-   完成像父进程发送命令

显然，在`fork`和写时复制时，主进程才会发生阻塞的可能。

# RDB快照

## 基本介绍

命令`save`和`bgsave`都是执行`rdb`快照的方法，前者在主进程执行，后者在子进程执行。`redis`还会每隔一段时间**后台**执行RDB，有如下配置：

-   `save 900 1`：900秒内，对数据库进行一次修改
-   `save 300 10`：300秒内，对数据库进行了10次修改
-   `save 60 10000`：60秒内，对数据库进行了10000次修改

`RDB`是全量快照，就是对整个数据库都进行快照。

## 方式

仍然利用写时复制的特性，`fork`出子进程来，保存的版本仍然是`fork`时候的版本，在写入磁盘时发生的改变，则不会更改。

## 缺点

显然，在子进程写入磁盘时，这时候出问题，就会造成主进程的数据丢失。

# RDB+AOF

## 基本介绍

在`redis.conf`中，可以有混合持久化的开关：

```
aof-use-rdb-preamble yes
```

`fork`出子进程后：

-   子进程将数据使用`RDB`方式写入，写入后并通知主进程

-   主进程依然把`fork`之后的命令写入到`AOF`缓冲区中，子进程结束后并把AOF缓冲区写入磁盘


持久化数据分成两部分，前半部分时RDB，后半部分时AOF。