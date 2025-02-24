# 信号量

信号量（Semaphore）是一种用于进程间或线程间通信与同步的机制。它主要用于控制对共享资源的访问权限，确保同一时间只有一个线程能够修改或访问该资源，从而避免竞争条件和数据不一致的问题。它一般只有两个操作，P操作和V操作。

信号量本质是一个计数器，一开始会被初始化为资源的数量，计数表示空闲资源。

-   P（等待）操作：计数不为0，不阻塞，计数减一，可以获取一个资源。计数为0，阻塞，等待计数不为0。
-   V（唤醒）操作：计数加一。

下面讲讲基本的API

```cpp
#include <semaphore.h>  // 头文件

sem_t sem;  // sem_t 是一个信号量结构体

// 信号量初始化
// 1. 传入信号量结构体指针
// 2. 0表示在一个进程中，1表示在多进程中使用
// 3. 资源的数量，也是信号量计数器初始值
sem_init(&sem, 0, 1);


// P操作
// 1. 传入信号量结构体地址
sem_wait(&sem);

// V操作
// 1. 传入信号量结构体地址
sem_post(&sem);

// 销毁信号量
// 1. 传入信号量结构体地址
sem_destory(&sem);
```

c++的STL也封装了

```cpp
#include <semaphore>
counting_semaphore<10> sm;  // 初始化10个信号量
sem.acquire();  // 获取，P操作，会阻塞
sem.release(1); // 释放，V操作，可以选择释放几个
sem.try_acquire();  // 尝试获取，P操作，不会阻塞，返回是否获取成功
sem.try_acquire_for(); // 尝试获取，P操作，会阻塞一定时间，返回是否获取成功
sem.try_acquire_until();  // 尝试获取，P操作，会阻塞直到某个时间，返回是否获取成功
sem.max();  // 返回最大值

```



# 互斥锁

互斥锁（Mutex）是一种同步机制，用于防止多个线程同时访问共享资源，从而避免数据竞争和其他并发问题。一个线程执行到获取锁的时候，会判断是否有锁，没锁的话会阻塞，有锁的话才会获取锁。执行完可能会冲突的代码之后，应该释放锁。

在c++中，有许多种互斥锁来实现不同的加锁解锁功能。

## 基本互斥锁

```cpp
#include <mutex>

std::mutex m;

m.lock(); // 加锁，返回void，阻塞
m.unlock(); // 解锁，返回void
m.try_lock(); // 尝试加锁，返回bool（表示加锁成功与否），不会阻塞
```

## 自旋锁

自旋锁的特点是，如果锁被占用，线程不会阻塞，而是一直循环等待，直到获取到锁，普通的锁是会阻塞的。

自旋锁适用于锁的持有时间短，而且线程不会被抢占的情况，如果锁的持有时间长，那么自旋锁会浪费大量的CPU资源，这时候就推荐使用普通锁。

```cpp
#include <mutex>
#include <atomic>

// 使用 atomic_flag 实现自旋锁

class spin_lock {
public:
    spin_lock() = default;
    spin_lock(const spin_lock&) = delete; 
    spin_lock& operator=(const spin_lock) = delete;
    void lock() {   // acquire spin lock
        while (flag.test_and_set()) {}
    }
    void unlock() {   // release spin lock
        flag.clear();
    }
private:
    atomic_flag flag;
};
```


## lock_guard

```cpp
#include <shared_mutex>

// 模板类，其实是为了简化互斥锁而已
template<typename _Mutex> class lock_guard;

mutex m;
lock_guard lg{m}; // 在lg的构造与析构时候会自动加解锁m
```

## recursive_mutex

```cpp
#include <mutex>

recursive_mutex m; // 可重入锁，如果一个线程会递归调用某个函数，但是其实都是这个线程在操作，并没有资源冲突，但是会重复获取锁锁导致死锁

// 所有操作与 mutex 一样，只是重入安全的
```

## timed_mutex

```cpp
#include <mutex>

// 是mutex的超集，多了两个成员函数而已
timed_mutex tm;


// 尝试获取锁，但是会阻塞 __rtime 时间
template <class _Rep, class _Period>
bool try_lock_for(const chrono::duration<_Rep, _Period>& __rtime);
bool ret = tm.try_lock_for(chrono::seconds(1));  // 阻塞1s

// 尝试获取锁，阻塞到某个时间
template <class _Clock, class _Duration>
bool try_lock_until(const chrono::time_point<_Clock, _Duration>& __atime);
bool ret2 = tm.try_lock_until( chrono::steady_clock::now() + 10s); // c++ 支持 "" 重载，所以10s是没问题的，当前时间+10s
```

## unique_lock

```cpp
#include <mutex>

// 也可以看作lock_guard的一个超集
// 可以手动加解锁
// 也可以使用 try_lock try_lock_for try_lock_until
// 还可以使用 owns_lock() 判断是否有锁
mutex m;
unique_lock ul(m, time);  // 没获取锁不会阻塞，可以选择传入时间来达到没获取锁先不急着阻塞的功能。
```

# 读写锁

由于很多时候，我们可能只需要获取某个资源的状态，而不需要对资源进行写入。这个时候其实除了写入的线程会影响变量的值，其他线程都是线程安全的，读写锁就是应对这种情况的。

c++中读写锁的功能是什么呢？对于一个锁，如果一个锁有线程进行了写锁，那么其他线程都无法获取这把锁。如果一个锁被任意个线程进行了读锁，其他线程仍然可以获取这把锁。

```cpp
#include <shared_mutex>

shared_mutex sm; // 读写锁（又叫做共享锁）
sm.lock();         	// 上写锁
sm.lock_shared();	// 上读锁，如果发现该锁已经有写锁了，就会阻塞，如果发现没有写锁，那么就不会阻塞
bool it1 = sm.try_lock();   // 尝试上写锁
bool it2 = sm.try_lock_shared();  // 尝试上读锁
sm.unlock();			// 解锁写锁
sm.unlock_shared();		// 解锁读锁的
sm.native_handle();  // 获取底层原生句柄的


// 下面是读锁，如果需要写锁，可以使用 unique_lock
shared_lock<shared_mutex> sl(sm); // 与unique_lock等价，也是一个包装器，用来管理读锁的，只会进行进行读锁
sl.lock();    // 这个也只是表示读锁
sl.release();
sl.try_lock();
sl.try_lock_for();
sl.try_lock_until();
sl.unlock();
```

# 条件变量

条件变量（condition_variable, cv）允许一个或多个线程等待另一个线程发出通知，以便进行线程同步，需要与互斥锁（mutex）一起使用。

cv有两个操作，一个是wait，一个是notify。

wait操作：等待 “通知线程” 发起通知，在wait期间，会释放锁

notify操作：通知 “等待线程” 已经ok了，如果是 notify_one 那么就会随机唤醒一个 “等待线程”， 如果是 notify_all 那么就会唤醒所有等待线程。

```cpp
#include <condition_variable>

// 包括两个：condition_variable 和 condition_variable_any
// condition_variable_any 的 wait 是模板，支持各种锁，而 condition_variable 只支持 unique_lock<mutex>
// condition_variable_any 灵活不高效，condition_variable 不灵活高效
mutex m;

unique_lock<mutex> ul;

condition_variable cv;
cv.native_handle();  // 获取句柄
cv.notify_all();	// 通知所有
cv.notify_one();	// 通知一个
cv.wait(ul, __p);			// 等待，也可以没有__p，即 cv.wait(m); 下面两个同理
cv.wait_for(ul, time, __p);		// 等待多长时间
cv.wait_until(ul, time, __p);	// 等待直到什么时候

// 解释上面的__p
void wait(unique_lock<mutex>& __lock);  // 这是wait的声明
// 下面是 wait(__lock, __p); 的声明，其中 unique_lock<mutex>& 已经不用管了，重点在于 _Predicate
// _Predicate表示是一个谓词，可以是函数，lambda表达式或者重载了()的类对象，可以根据 __p() 看得出，必须能够调用()
template<typename _Predicate> 
void wait(unique_lock<mutex>& __lock, _Predicate __p)
{
	while (!__p()) wait(__lock);
}
// 下面看看具体解释
```

很多实际应用中，发现了`wait(lock)`并不能完全卡住，有时候出现了虚假唤醒，为了避免这种情况，往往会增加一个条件来使得线程完全卡住。即：

```cpp
bool ready = false;  // 标识条件的
while(not ready){  // ready是 “通知线程” 来改变的值，其他线程不会改变，如果 “通知线程” 没有准备好，则会继续进入该循环，避免虚假唤醒
    wait(lock);  // 有可能不能完全卡住
}
// 结合上面，发现和 wait(lock, __p); 非常像，所以尽量采用 wait(lock, __p); 的方式
```

那么一般如何写这个`__p`呢，通常建议使用一个全部线程都可访问的变量 `bool ready`，然后用lambda即可。（为了避免不必要的麻烦，保证`__p`是满足线程安全的）

# 原子操作

原子操作应该是多线程中比较简单的一个东西了，封装了大部分的类型，直接使用这些对象即可保证线程安全。

```cpp
#include <atomic>

template<class T>
atomic<T> a; // 这里的T支持 指针，智能指针，常用的数据类型

```

`atomic`对所有的整型（int8-int64）做了优化，对地址也做了优化，其重载了常用的计算符号，比如`+-*/|&~`等等。

下面需要讲讲另一个问题，会发现有许多`fetch_*`的函数，有两个参数。其实重载的运算符也是调用的这些函数，但是第二个使用了默认值（`seq_cst`）而已。

```cpp
fetch_*(VALUE value, memory_order mem);  // 第一个值就是 value就不说了，重点看第二个
```

内存序（memory_order）是处理多线程环境下内存操作的重要概念。它决定了原子操作之间如何有序地执行，以确保程序的正确性和一致性。



对于单线程来说，下面代码也会是乱序的（汇编层面）

```cpp
int a = 1;  // 将1赋值给a（A）
int b = a + 2; // 将a移入寄存器（B），将寄存器值+2（C），将寄存器值赋值给b（D）
a = 4; // 将4赋值给a（E）

// 上面如果不优化顺序，那么执行就是 A B C D E 五个操作，但是其实实际上来说，顺序可能会变
// A B E C D 其实也不会改变最终结果，又或者 A B C E D也是同样结果

// 但是假如 b=a+2是原子顺序的，就顺序一定是 A BCD E执行。

// ps：这种优化的作用在何处呢，可以充分利用CPU寄存器及缓存等资源
```

在单线程中，编译器完全可以帮你优化代码，并保证正确的执行顺序（即最终结果不变），但是在多线程中，可能会有问题，比如下面代码：

```cpp
void thread_A(){
    atomic_A1();
    atomic_A2();
}
void thread_B(){
    atomic_B1();
    atomic_B2();
}
```

即使已经保证了原子顺序，两个线程的四个执行顺序仍然有很多不同：`A1,A2,B1,B2`, `A1,B1,A2,B2`等等。如果两个线程相互独立，那么什么执行顺序都可以，只需要保证单线程中的执行顺序即可。

但是，如果`B1`依赖于`A1`呢，那么执行顺序也就变的重要了。





下面是c++中的内存序最基本的定义，并在消费者生产者模型进行应用解释。

```cpp
enum class memory_order : int
{
    relaxed,  // 宽松序，不提供任何同步保证，仅保证操作的原子性
    
    consume,  // 消费者序，与acquire很像，很多地方两者实现一致
    
    acquire,  // 获取序，本次读原子操作之后的读写顺序不会排到操作之前，保证读取资源是最新的，比如消费者线程必须要这个序。atomic::load()使用很好
    release,  // 释放序，本次写原子操作之后的读写顺序不会排到操作之前，保证别人获取的资源是最新的，比如生产者线程必须要这个序。atomic::store()使用很好
    acq_rel,  // 获取-释放序，acquire-release两者的叠加
    
    seq_cst   // 顺序一致，最强的顺序保证，所有操作都按照顺序执行
};

// 一般用下面的，比如 memory_order_*
inline constexpr memory_order memory_order_relaxed = memory_order::relaxed;
inline constexpr memory_order memory_order_consume = memory_order::consume;
inline constexpr memory_order memory_order_acquire = memory_order::acquire;
inline constexpr memory_order memory_order_release = memory_order::release;
inline constexpr memory_order memory_order_acq_rel = memory_order::acq_rel;
inline constexpr memory_order memory_order_seq_cst = memory_order::seq_cst;
```

# 屏障

屏障就是等待线程到达某个地方，介绍一下Linux的基本`api`，同样也可以使用原子操作，条件变量和互斥锁等实现屏障。

```cpp
#include <pthread.h>

pthread_barrier_t bt;  // 屏障体
pthread_barrier_init(&bt, nullptr, 10);  // 初始化屏障体，第三个参数表示等待多少个线程到达才继续执行

pthread_barrier_wait(&bt); // 由线程调用，一方面是告诉这个屏障体我到达了，另一方面是在改处等待足够的线程数达到

pthread_barrier_destroy(&bt);  // 摧毁屏障体
```



