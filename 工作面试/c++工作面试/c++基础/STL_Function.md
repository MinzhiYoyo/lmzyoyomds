# Function

`std::function`类是一个包裹可调用对象的类，他是一个模板类，c++中可调用对象有，函数、函数指针、lambda表达式、函数对象以及bind对象。（所有可以使用括号`()`调用的都是可调用对象）

怎么包裹呢？

```cpp
// 1. fun 是一个函数
int fun(int a, int b);       

// 2. fun_pointer 是一个函数指针
int (*fun_pointer)(int, int);  

// 3. fun_lambda 是一个lambda表达式
auto fun_lambda = [](int a, int b) -> int {}; 

// 4. // fun_object就是一个函数对象，因为其类重载了()
class Fun_class{
    int operator()(int a, int b);
};
Fun_class fun_object;  

// 5. 跳过 bind 对象先

// 如何绑定上面五种对象呢？
std::function<int(int, int)> f1(fun);
std::function<int(int, int)> f2(fun_pointer);
std::function<int(int, int)> f3(fun_lambda);
std::function<int(int, int)> f4(fun_object);
// 跳过 bind 对象

//// 上面的 f1 f2 f3 f4 就是包裹的 function 对象了，基本上用法也只需要关注 () 即可
f1(1, 2);
f2(3, 4);
f3(4, 5);
f4(5, 6);

// 还有一个用法就是，operator bool，可以判断该对象是否可调用
if(f1){
    f1();
}else{
    cout << "不可调用" << endl;
}
```

# bind

## 简单用法

`bind`是一个函数，传入另一个可调用对象，返回一个新的可调用对象，它的原型非常复杂，这里不做解释了，但是下面给一个伪原型吧

```cpp
function_type bind(function_pointer, Class_Object*, args...);  // 传入一个函数指针，以及一个类的对象地址
function_type bind(function_pointer, args...);  // 只传入一个函数指针

// args... 是可变参数，常常对应于函数指针的参数
```

1.   绑定函数

```cpp
int function_1(int a, int b, int c, int d) {
    std::cout << "调用了 function_1 函数" << std::endl;
    std::cout << "传入参数：[" << a << ", " << b << ", " << c << ", " << d << "]" << std::endl;
    int sum = a + b + c + d;
    std::cout << "返回值：" << sum << std::endl;
    return sum;
}

// f1 叫做 bind 对象
auto f1 = std::bind(function_1, 1, 2, 3, 4);  // 后面的参数个数一定要和原函数对应
f1("乱传参");  // 真正执行f1的时候，这里可以随意传参，但是没什么用的，并不会报错，正常执行

// f1的类型：
std::_Bind_helper<false, int(&)(int, int, int, int), int, int, int, int>::type f1; // 所以还是建议用auto吧
```

2.   绑定函数指针

```cpp
int function_2_(int a, int b, int c, int d) {
    std::cout << "调用了 function_2 函数" << std::endl;
    std::cout << "传入参数：[" << a << ", " << b << ", " << c << ", " << d << "]" << std::endl;
    int sum = a + b + c + d;
    std::cout << "返回值：" << sum << std::endl;
    return sum;
}
int (*function_2)(int, int, int, int) = function_2_;
auto f2 = std::bind(function_2, 1, 2, 3, 4);
f2();  // 真正执行

// f2的类型
std::_Bind_helper<false, int(*&)(int, int, int, int), int, int, int, int>::type f2;
```

3.   绑定成员函数和成员变量：没看错，可以绑定到变量上面

```cpp
class FC {
    int data_1{16};
public:
    int data_2{18};
    int operator()(int a, int b, int c, int d) const {
        std::cout << "调用了 operator() 函数" << std::endl;
        std::cout << "传入参数：[" << a << ", " << b << ", " << c << ", " << d << ", " << data_1 << ", " << data_2 << "]" << std::endl;
        int sum = a + b + c + d + data_1 + data_2;
        std::cout << "返回值：" << sum << std::endl;
        return sum;
    }

    int Function(int a, int b, int c, int d) const {
        std::cout << "调用了 FC::Function() 函数" << std::endl;
        std::cout << "传入参数：[" << a << ", " << b << ", " << c << ", " << d << ", " << data_1 << ", " << data_2 << "]" << std::endl;
        int sum = a + b + c + d + data_1 + data_2;
        std::cout << "返回值：" << sum << std::endl;
        return sum;
    }
};

FC function_3;

// 直接传 function_3 还是跟一个可调用对象一样，所以不需要指针
auto f3_1 = std::bind(function_3, 1, 2, 3, 4);
// 传入成员函数/变量地址和对象地址
auto f3_2 = std::bind(&FC::Function, &function_3, 1, 2, 3, 4);
auto f3_3 = std::bind(&FC::data_2, &function_3);

f3_1(); 
f3_2();
f3_3();  // 返回 int

std::_Bind_helper<false, FC &, int, int, int, int>::type f3_1;
std::_Bind_helper<false, int(FC::*)(int, int, int, int) const, int, int, int, int>::type f3_2;
std::_Bind_helper<false, int FC::*, FC *>::type f3_3;
```

4.   绑定lambda表达式

```cpp
auto function_4 = [](int a, int b, int c, int d) {
        std::cout << "调用了 lambda 函数" << std::endl;
        std::cout << "传入参数：[" << a << ", " << b << ", " << c << ", " << d << "]" << std::endl;
        int sum = a + b + c + d;
        std::cout << "返回值：" << sum << std::endl;
        return sum;
};
auto f4 = std::bind(function_4, 1, 2, 3, 4);
f4();

std::_Bind_helper<false, lambda int(int a, int b, int c, int d)> &, int, int, int, int>::type f4;
```



## 用途

光像上面这样，其实就是一个用途，把一个 `int(int, int, int, int)` 对象转换成了 `int()` 对象，但是加上占位符，就更灵活了，这里只用一个例子来解释即可

```cpp
// 不知道还记得前面 f1("乱传参"); 吗，这里不会报错，接下来说清楚一点

auto f1 = std::bind(function_1, 1, 2, 3, 4);  
```

任何 bind 函数后面的参数都是可变的，但是这样传进去固定了，就没意义了，因此可以用占位符来控制参数，`std::placeholders::_1`，占位符后面是`_1`，表示`bind`对象的第一个参数会使用到这里，看下面例子

```cpp
int function_1(int a, int b, int c, int d);
auto f1 = std::bind(function_1, 1, 2, std::placeholders::_1, 4); 
// 说明了 f1 对象的第三个参数即 f1(1, 3, 5, 3,4); 中的5 被绑定到了 function_1的c上面
f1(1, "乱写", 5, 3); // 实际上 c就等于5了

// f1类型是
std::_Bind_helper<false, int(&)(int, int, int, int), int, int, const std::_Placeholder<1> &, int>::type f1;
```

有没有更灵活的，当然，没说一定要按顺序来的

```cpp
int function_1(int a, int b, int c, int d);
auto f1 = std::bind(function_1, 1, 2, std::placeholders::_4, std::placeholders::_2);
f1(10, 20, 30, 40);  // 那么 d=20, c=40

// 也就是，占位符_数字是将bind对象对应的绑定的原函数对应位置
// 但是最大的占位符数字表示 f1()调用最少要传多少个参数，绑定位置不能传错参数，其他位置无所谓。
```

## 占位符

这样的占位符，提供了如下29个，显然不够可以自己定义：

```cpp
  namespace placeholders
  {
    _GLIBCXX_PLACEHOLDER const _Placeholder<1> _1;
    _GLIBCXX_PLACEHOLDER const _Placeholder<2> _2;
    _GLIBCXX_PLACEHOLDER const _Placeholder<3> _3;
    _GLIBCXX_PLACEHOLDER const _Placeholder<4> _4;
    _GLIBCXX_PLACEHOLDER const _Placeholder<5> _5;
    _GLIBCXX_PLACEHOLDER const _Placeholder<6> _6;
    _GLIBCXX_PLACEHOLDER const _Placeholder<7> _7;
    _GLIBCXX_PLACEHOLDER const _Placeholder<8> _8;
    _GLIBCXX_PLACEHOLDER const _Placeholder<9> _9;
    _GLIBCXX_PLACEHOLDER const _Placeholder<10> _10;
    _GLIBCXX_PLACEHOLDER const _Placeholder<11> _11;
    _GLIBCXX_PLACEHOLDER const _Placeholder<12> _12;
    _GLIBCXX_PLACEHOLDER const _Placeholder<13> _13;
    _GLIBCXX_PLACEHOLDER const _Placeholder<14> _14;
    _GLIBCXX_PLACEHOLDER const _Placeholder<15> _15;
    _GLIBCXX_PLACEHOLDER const _Placeholder<16> _16;
    _GLIBCXX_PLACEHOLDER const _Placeholder<17> _17;
    _GLIBCXX_PLACEHOLDER const _Placeholder<18> _18;
    _GLIBCXX_PLACEHOLDER const _Placeholder<19> _19;
    _GLIBCXX_PLACEHOLDER const _Placeholder<20> _20;
    _GLIBCXX_PLACEHOLDER const _Placeholder<21> _21;
    _GLIBCXX_PLACEHOLDER const _Placeholder<22> _22;
    _GLIBCXX_PLACEHOLDER const _Placeholder<23> _23;
    _GLIBCXX_PLACEHOLDER const _Placeholder<24> _24;
    _GLIBCXX_PLACEHOLDER const _Placeholder<25> _25;
    _GLIBCXX_PLACEHOLDER const _Placeholder<26> _26;
    _GLIBCXX_PLACEHOLDER const _Placeholder<27> _27;
    _GLIBCXX_PLACEHOLDER const _Placeholder<28> _28;
    _GLIBCXX_PLACEHOLDER const _Placeholder<29> _29;
  }

```

# 结合bind和function

值得注意的是，四种类型不一样，不能相互赋值

```cpp
f1 = f2; // 会报错，因为两者类型不一样
```

之前的定义中，都是用的`auto`关键字，但其实可以直接用`function`类来定义，这样就可以保证类型一样，能够赋值了

```cpp
std::function<int(int)> f1_1 = std::bind(function_1, 1, std::placeholder::_1, 3, 4);
std::function<int(int)> f2_1 = std::bind(function_2, 1, 2, std::placeholder::_1, 4);
f1_1(10); // 执行 function_1 函数
f1_1 = f2_1;
f1_1(10);  // 执行 function_2 函数
```

有什么需要注意的呢？

```cpp
std::function<int(int)> // 表示传入参数只有一个 int 型
std::bind(function_1, 1, std::placeholder::_1, 3, 4); // 只有一个占位符，因此前面的std::function中也只能有一个参数

f1_1(10); // 必须跟 function 保持一致
```

# 总结与用途

本文讲了`bind`和`function`两个用法，`function`是一个函数包装器，`bind`是将一个可调用对象绑定参数后再返回一个函数包装器（`function`或者`_Bind_helper`），绑定参数时可以使用占位符，来改变最终函数包装器的调用方法（即参数个数可以不同）。

一个简单用法如下：

```cpp
std::vector<std::function<void()>> fs;  // 定义一个可调用对象数组

// 定义两个函数
void function_1(int a, std::string b);
void function_2(std::vector<int>& a, float b);

// 把两个函数绑定到对象数组上面
fs.emplace_back(
    std::bind(function_1, 10, "hello")
);
fs.emplace_back(
    std::bind(function_2, vec, 3.14)
);

// 其他获得 fs 访问权的代码都可以调用这两个函数
fs[0]();
fs[1]();

// 在定义线程池中，这是一个很好的用法
```

