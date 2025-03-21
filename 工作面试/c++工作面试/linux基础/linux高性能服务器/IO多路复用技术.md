# 从Socket出发

## Socket介绍

socket叫做套接字，是传输层的东西，可以用来 `IPv4/IPv6 - TCP/UDP`通信。socket是CS模型，即客户端服务器模型。

服务器建立socket的过程如下：

```cpp
socket(IPv4/IPv6, TCP/UDP);  // 创建socket，并且指定是哪种协议
bind(IP, port);	// 将应用程序绑定的IP地址和端口上，IP地址是因为有多个网卡，只监听感兴趣的网卡就行，端口是一个端口之后被监听到一个应用程序上
listen(); // 开始监听
accept(); // 建立连接
read() / write(); // 读写操作
close(); // 释放连接
```

客户端的socket过程为：

```cpp
socket();
connect(); // -> accept()
read() / write(); // 读写
close();
```

## 文件描述符

上述socket中会返回一个文件描述符，在文件读写中也发现有个文件描述符，这是因为`Linux`万物皆文件的思想。

下面简单介绍文件描述符：

>   Linux每个进程都有一个数据结构，task_struct，其中有一个数组（指针）。该数组下标就是文件描述符，数组内容就是文件地址。
>
>   每个文件都有元数据（创建时间，创建者等），元数据存储的地方叫做`inode`，可以使用`df -i`命令查看每个文件系统的`inode`信息，`inode`是预先分配了一大块硬盘存储的（约总容量的百分之一），`inode`有唯一的标识
>
>   Socket的inode指向一个Socket结构体，这里面有两个队列，发送和接收，队列里的元素是 struct sk_buff; 这个sk_buff 保存的是头地址，数据地址，尾地址，结束地址。指向一个网络frame的各个位置，用来在不同TCP/IP层中去包头去包尾，将数据移交上层协议只需要更改data地址和tail地址就行。

## 多进程Socket

传统方式是，采用多进程来处理大量的socket连接，主进程接收到连接`accept()`后，就`fork()`一个子进程用来处理连接。子进程会复制父线程的所有东西，包括文件描述符，因此子进程可以直接使用已经建立好的连接进行通信，不需要重新建立。

## 多线程Socket

进程切换太慢，所以需要线程。由于是同一个进程的，所以只需要把文件描述符发送给子线程即可。

# IO多路复用

如果一个连接需要一个专门的进程/线程，那么一般一台机器需要处理1万个连接（称为C10K）该怎么办呢？创建1w个连接显然对一台机器来说不太合适。显然，一个线程如果在读数据的时候其实是在等待的，可不可以选择压榨压榨线程，让它这个时候去处理其他连接的数据呢？也就是将连接建立-读数据-处理-写数据分离。这种方式就是**IO多路复用技术**。

IO多路复用总结来说就是，应用程序通过系统调用，获取有数据需要读的文件描述符，然后再读取数据。

传统的多路复用是`select/poll`，在Linux2.5.44及其之后，引入了epoll。

# select

监听文件描述符集合采用数组存储，有最大监听上限。

应用程序将集合拷贝至系统，系统会轮询集合，查看有没有读写或异常事件发生。

```cpp
#include <select.h>

int select (  // 返回值，如果-1表示出问题了，如果是自然数，表示有多少个事件ok了
    int __nfds,   // 文件描述符范围，为最大文件描述符+1或者为0
    fd_set *__restrict __readfds,   // 读事件的集合，nullptr则忽略检测
    fd_set *__restrict __writefds,  // 写事件的集合，nullptr则忽略检测
    fd_set *__restrict __exceptfds, // 错误事件集合，nullptr则忽略检测
    struct timeval *__restrict __timeout // 超时时间，#include <struct_timeval.h>
); 

typedef struct
{
    typedef long int __fd_mask;
    // __FD_SETSIZE = 1024
    // __NFDBITS = (8 * (int) sizeof (__fd_mask))
    __fd_mask fds_bits[__FD_SETSIZE / __NFDBITS];
} fd_set;

struct timeval
{
    using __time_t = long int;
    using __suseconds_t = long int;
    __time_t tv_sec; // seconds
    __suseconds_t tv_usec; // microseconds
};
```

如何使用select呢？假设已经有了文件描述符

```cpp
// 文件描述符
int fds[NUMBER]; // 假设已经定义了文件描述符了

// 初始化读取事件 fd_set，有以下辅助宏，其他同理
FD_CLR(fd, fd_set*)  // 将fd在set中删除
FD_SET(fd, fd_set*)  // 将fd在set中增加
FD_ZERO(fd_set*) // 清空set
FD_ISSET(fd, fd_set*)// 检测fd是否被set了
    
fd_set readfds;
FD_ZERO(&readfds);
for (int i = 0; i < NUMBER; ++i){
    FD_SET(fds[i], &readfds);
}

struct timeval timeout = {1, 0}; // 监听1s

int ret = select(0, &readfds, nullptr, nullptr, &timeout); // 在这里最多阻塞1s
if(ret > 0){
    for(int i = 0; i < NUMBER; ++i){
        if(FD_ISSET(i, &readfs)) {
            read(i, buf, N);  // 读取数据，当然这里的buf和N是乱写的
        }
    }
}
```



# poll

相比于select不同之处仅在于使用链表存储集合，没有上限。

```cpp
#include <poll.h>

int poll (  // 返回-1错误，返回其他表示数量
    struct pollfd *__fds, // pollfd结构体，定义某个文件描述符需要监听的事件
    nfds_t __nfds,     // 文件描述符范围，为最大文件描述符+1或者为0
    int __timeout      // 阻塞的时间，大于0的值表示最多阻塞 __timeout milliseconds，-1表示一直阻塞直到一个事件发生
);

typedef unsigned long int nfds_t;

struct pollfd
{
    int fd;			  	// 文件描述符
    short int events;	// poller应该关注的事件
    short int revents;	// 实际发生了的事件，用于应用程序检查
};

#define POLLIN		0x001		/* 读取事件  */
#define POLLPRI		0x002		/* 要紧数据读取  */
#define POLLOUT		0x004		/* 写入数据  */

# define POLLRDNORM	0x040		/* 普通数据读取  */
# define POLLRDBAND	0x080		/* 优先级数据读取  */
# define POLLWRNORM	0x100		/* 普通数据写入  */
# define POLLWRBAND	0x200		/* 优先级数据写入  */
```

下面是使用示例

```cpp
// 文件描述符
int fds[NUMBER]; // 假设已经定义了文件描述符了

struct pollfd pollfds[NUMBER];
for(int i = 0; i < NUMBER; ++i){
    pollfds.fd = i;
    pollfds.events = POLLIN;
}

int ret = poll(fds, NUMBER, -1); 
if(ret > 0){
    for(int i = 0; i < NUMBER; ++i){
        if(fds[i].revents & POLLIN != 0){  // 检验 POLLIN 位置
            raed(i, buf, N); // 读取
        }
    }
}
```

# epoll

使用了上述两种方案发现，用起来都比较简单，然后在内核也好，在应用程序也好，如果需要找到有事件发生的`fd`，都需要`O(n)`的复杂度来遍历，而epoll有如下两个特点，优化了上面的技术

1.   底层用红黑树存储文件描述符集合
2.   使用队列存储就绪就绪文件描述符集合

所以，极大优化了IO多路复用技术

```cpp
#include <sys/epoll.h>
// 主要有三个函数
// 1. 初始化一个epoll
int epoll_create (int __size);  // __size表示有多少个文件描述符，返回epoller标识符

// 2. 注册
// 返回0成功，返回-1失败
// __epfd 是 epoller 标识
// __op 是 epoll 的控制选项
// __fd 是文件描述符
// __event 是监听事件
int epoll_ctl (int __epfd, int __op, int __fd, struct epoll_event *__event);

// 3. 获取
// __epfd 是 epoller 标识
// __events 是返回的事件队列的地址
// __maxevents 是返回的事件数量最大值，不能大于__events的大小，通常设置成__events的大小
// __timeout 是阻塞的时间，大于0的值表示最多阻塞 __timeout milliseconds，-1表示一直阻塞直到一个事件发生
// 返回-1表示出错，否则表示返回事件队列的数量
int epoll_wait (int __epfd, struct epoll_event *__events, int __maxevents, int __timeout);

// epoll_ctl 参数 __op 取值
#define EPOLL_CTL_ADD 1	/* 增加事件     */
#define EPOLL_CTL_DEL 2	/* 删除事件     */
#define EPOLL_CTL_MOD 3	/* 改变事件属性  */

struct epoll_event
{
  uint32_t events;	/* Epoll 事件(enum EPOLL_EVENTS) */
  epoll_data_t data;	/* 数据 */
};
typedef union epoll_data
{
  void *ptr;
  int fd;
  uint32_t u32;
  uint64_t u64;
} epoll_data_t;

enum EPOLL_EVENTS
  {
    EPOLLIN = 0x001,     		// 可读
    EPOLLPRI = 0x002,			// 紧急数据读
    EPOLLOUT = 0x004,			// 可写
    EPOLLRDNORM = 0x040,		// 普通数据读取
    EPOLLRDBAND = 0x080,		// 紧急数据读取
    EPOLLWRNORM = 0x100,		// 普通写入
    EPOLLWRBAND = 0x200,		// 紧急写入
    EPOLLMSG = 0x400,
    EPOLLERR = 0x008,
    EPOLLHUP = 0x010,
    EPOLLRDHUP = 0x2000,
    EPOLLEXCLUSIVE = 1u << 28,
    EPOLLWAKEUP = 1u << 29,
    EPOLLONESHOT = 1u << 30,
    EPOLLET = 1u << 31
  };
```

如何使用呢？

```cpp
// 文件描述符
int fds[NUMBER]; // 假设已经定义了文件描述符了

int epfd = epoll_create(NUMBER);

struct epoll_events evs[NUMBER];
// 初始化监听事件
for(int i = 0; i < NUMBER; ++i){
    evs[i].events = EPOLLIN;
    evs[i].data.fd = fds[i];
    epoll_ctl(epfd, EPOLL_CTL_ADD, fds[i], &evs[i]);
}

struct epoll_events revs[NUMBER]; // 存储发生的事件
int n = epoll_wait(epfd, revs, NUMBER, -1); 
if(n > 0){
    for (int i = 0; i < n; ++i){  // 只需要遍历发生了的事件，不需要遍历所有
		auto can_read = revs[i].events & EPOLLIN != 0;
        auto fd = revs[i].data.fd;
        read(fd, buf, N); // 读取数据
    }
}
```

# 触发模式

上面例子只调用了一次`select(), poll()`或者`epoll_wait()`，但是在实际中肯定会重复调用的。对于前两种，假如`fds[3]`的事件在上一次调用中还没被处理，在下一次中它仍然会被触发直到事件处理结束（LT水平触发）。但是对于后一种，因为返回了事件列表，所以只会触发一次（ET边沿触发）。



