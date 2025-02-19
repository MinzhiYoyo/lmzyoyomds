# 现代cpp教程

# 模板

模板可以说是cpp一个非常重要的东西，重要到什么地步呢？所有的库，基本上都需要模板实现。下面来说说一些重要概念

1.   模板是生成类的，也就是说，如果定义了一个模板，然后代码中实现了若干个这样的类，编译器会自动生成若干个实际类，并且是在编译期间实现的，因此，单纯的模板是无法生成静态动态库的。
2.   

# 类型转换

使用`static_cast, reinterpret_cast, const_cast， dynamic_cast`来进行类型转换

-   `dynamic_cast`：将基类指针转换为派生类

    >   ```cpp
    >   class A {
    >       
    >   }
    >   class B: public A {
    >       
    >   }
    >   A* ptrA = new A();
    >   B* ptrB = dynamic_cast<B*>(ptrAs);
    >   
    >   ```

-   `static_cast & reinterpret_cast`：`static_cast`会进行类型转换，而`reinterpret_cast`是不会进行类型转换，而是进行比特位的完全复制。比如一个`float`转换成`int`（如`3.14159`），前者会转换成`3`，后者就只是把对应的比特位拷贝了，并且仅仅转换指针，所以后者也尝尝用于不同类型的指针转换。

    >   ```cpp
    >   float f32 = 3.1415926;
    >   double f64 = 3.1415926;
    >   int i32 = 3;
    >   long i64 = 3;
    >   
    >   int f32_to_i32 = static_cast<int> (f32);
    >   int f64_to_i32 = static_cast<int> (f64);
    >   int f32_to_i32_reinterpret = *reinterpret_cast<int*> (&f32);
    >   int f64_to_i32_reinterpret = *reinterpret_cast<int*> (&f64);
    >   auto f32_to_i32_auto = reinterpret_cast<int*> (&f32);
    >   
    >   cout << "f32: " << f32 << endl;
    >   cout << "f64: " << f64 << endl;
    >   cout << "i32: " << i32 << endl;
    >   cout << "i64: " << i64 << endl;
    >   cout << "f32_to_i32: " << f32_to_i32 << endl;
    >   cout << "f64_to_i32: " << f64_to_i32 << endl;
    >   cout << "f32_to_i32_reinterpret: " << f32_to_i32_reinterpret << endl;
    >   cout << "f64_to_i32_reinterpret: " << f64_to_i32_reinterpret << endl;
    >   cout << "f32_to_i32_auto: " << *f32_to_i32_auto << endl;
    >   
    >   /** 输出如下：
    >   f32: 3.14159
    >   f64: 3.14159
    >   i32: 3
    >   i64: 3
    >   f32_to_i32: 3
    >   f64_to_i32: 3
    >   f32_to_i32_reinterpret: 1078530010
    >   f64_to_i32_reinterpret: 1293080650
    >   f32_to_i32_auto: 1078530010
    >   */
    >   ```

-   `const_cast`：用于消除变量的`const`属性，但是要注意编译器的优化导致结果可能和想象的不一样。

    >   ```cpp
    >   #include<iostream>
    >   using namespace std;
    >   void ConstTest1(){
    >       const int a = 1;
    >       int *p;
    >       p = const_cast<int*>(&a);
    >       (*p)++;
    >       cout<<a<<endl;
    >       cout<<*p<<endl;
    >       
    >   }
    >   void ConstTest2(){
    >       int i=3;
    >       const int a = i;
    >       int &r = const_cast<int &>(a);
    >       r++;
    >       cout<<a<<endl;
    >   }
    >   int main(){
    >       ConstTest1();
    >       ConstTest2();
    >       return 0;
    >   }
    >   
    >   /** 输出如下：
    >   1
    >   2
    >   4
    >   */
    >   ```
    >
    >   `const int a = 1;`编译器直接用`1`来替代了a，所以会导致这个问题

# 异常处理

使用`noexcept`，不要使用`unexpected_handler, set_unexpected()`

`noexcept`用来表示函数是否抛出异常

```cpp
void function() noexcept {  // noexcept等同于noexcept(true)
    throw 1;
}
void function() noexcept(false) {  // 依然会抛出异常
    throw 1;
}
int main() {
    try{
	    function();
    } catch (...) {
        std::cout << "error" << std::endl;
    }
    return 0;
}
/*
程序会直接退出，而不是进入 std::cout 打印
*/
```

# 智能指针

使用`unique_ptr`而不是`auto_ptr`

下面展示一下智能指针的用法，包括`unique_str, shared_ptr, weak_ptr`

-   `unique_ptr`：无法进行左值复制和构造，只能进行右值复制和构造

# 变量初始化

可以在`if, switch`中声明临时变量了，学习go大法好

```cpp
vector<int> vec = {1, 2, 3}
if (auto itr = find(vec.begin(), vec.end(), 3); itr != vec.end()){
    // do something
}

switch (int i = j * 3 + 2; i){
    case 1:
        cout << "1" << endl;
        break;
    default:
        cout << "default" << endl;
}
```

# 元组用法

```cpp
#include <iostream>
#include <tuple>

std::tuple<int, double, std::string> f() {
    return std::make_tuple(1, 2.3, "456");
}

void f(){
    auto [x, y, z] = std::tuple<int, std::string, float>(1, "hello", 3.14);
    std::cout << x << " " << y << " " << z << std::endl;
}

int main() {
    auto [x, y, z] = f();  // c++17之后，元组可以直接使用[]来绑定了，并且用auto关键字，简直不要太爽
    std::cout << x << ", " << y << ", " << z << std::endl;
    return 0;
}
```



# 类型推导

c++11之后引入了`auto, decltype`两个关键字来进行类型推导

`auto`用法：

```cpp
int add(auto a, auto b, auto c) {  // 推导类型
    c = "string";
    return a+b;
}
for(auto iter = vec.begin(); iter != vec.end(); ++iter){}  // 简化代码
```

`decltype`用法：

```cpp
auto x = 1;
auto y = 1;
decltype(x+y) z;  // 相当于 int z;
if(std::is_same_v<decltype(x), int>) {
    std::cout << "type x == int" << std::endl;
}

// 现代 c++ 引入了尾返回类型，喜欢我现代c++的即视感吗？
template<typename T, typename U>
auto after_cpp_11(T x, U y) -> decltype(x+y){
    return x + y;
}
template<typename T, typename U>
auto after_cpp_14(T x, U y){
    return x + y;
}
```

`decltype(auto)`大法：

```cpp
std::string  lookup1();
std::string& lookup2();

// 非现代cpp
std::string look_up_a_string_1() {
    return lookup1();
}
std::string& look_up_a_string_2() {
    return lookup2();
}

// 现代cpp
decltype(auto) look_up_a_string_1() {
    return lookup1();
}
decltype(auto) look_up_a_string_2() {
    return lookup2();
}
```



# 变长参数模板

变长参数模板是cpp的一个黑魔法，Dark Magic。变长参数通常使用符号`...`来标注，其与引用指针一样，写在类型与变量中间

```cpp
template<typename... Args> class Magic;  // 变长参数模板类
template<typename... Args> void darkMagic(Args... args);  // 变长参数模板函数
```

既然是变长，那么零个参数也是可以的，比如：

```cpp
Magic<> magic;  // 使用零个参数的变长模板类
darkMagic<>();  // 使用零个参数的变长模板函数
```

与此同时，提供了一个运算符`sizeof...`来获取参数的个数

```cpp
template<typename.. Args> 
void darkMagic(Args... args){
    std::cout << sizeof...(args) << endl;
}
```

如何合理的运用这些参数呢，通常变长参数模板是使用递归的方式来实现，并在c++17中引入了变长参数模板的一个支持，比如：

```cpp
template<typename Arg0, typename ...Args>
void darkMagic(Arg0 arg0, Args ...args){
    process(arg0);
    if constexpr (sizeof...(args) > 0) {
        darkMagic(args...);  // 将args进行展开
    }
}
```

学了上面，差不多能够很好的运用变成参数模板类，下面再来一个更抽象的黑魔法，我们知道运算符`...`是表示包展开的意思，那么就可以这么来

```cpp
template<typename ...Args>
void darkMagic(Args... args) {
    int arr[] = {  // 所有支持 {} 初始化的都可以来展开包
        args...
    };
    
    for (int i = 0; i < sizeof...(args); ++i) {
        std::cout << a[i] << std::endl;
    }
}

// 调用
darkMagic(1, 2, 3, '3', 4.5);  // '3'会隐式转换成51, 4.5会隐式转换成4
// 但是如果不能隐士转换成 int 类型的，就会编译报错，比如
darkMagic(1, 2, 3, "hello");
// 这是由于，int arr[] = {1, 2, 3, "hello"}; 中 "hello" 是  const char * ，无法转换成 int 
```

介于上述展开特性，我们可以借助逗号表达式`,`来完成一个特别抽象的操作，先讲讲什么是逗号表达式

```cpp
// 逗号表达式： e1, e2
// 程序会先运行 e1，然后运行e2，但是整个表达式的值是 e2 的运行值
// 根据这个特性，来实现一些很有趣的操作，比如
int a = ("hello", 2);  // 逗号表达式的优先级很低，所以需要加括号，这里 a=2
// 那么是不是也可以写
int a[] = {
    (1, 0),
    (2, 0),
    ("hello", 0),
    ('c', 0)
};
```

因此，修正上面的黑魔法

```cpp
template<typename ...Args>
void darkMagic(Args... args) {
    int a[] = {
        (args, 0)...
    };
    for (int i = 0; i < sizeof...(args); ++i) {
        std::cout << a[i] << std::endl;  // 输出若干个0
    }
}

darkMagic(1, 2, 3, 4, 5, "hello", "worl", 'd');  // 不会报错，编译成功
```



当然，目前做到这里没有什么意义，这是因为，我们没法保存这些变量值，可不要忘记，这个逗号表达式前后都是表达式，那就可以写代码了，继续修正

```cpp
template<typename ...Args>
void darkMagic(Args... args) {
    int a[] = {
        (std::cout << args << ' ', 0)...
    };
    for (int i = 0; i < sizeof...(args); ++i) {
        std::cout << a[i] << std::endl;  // 仍然打印的是0
    }
}

darkMagic(1, 2, 3, 4, 5, "hello", "worl", 'd'); // 能够打印出来了
```

除此之外，不妨借助lambda来实现更多的操作

```cpp
template<typename ...Args>
void darkMagic(Args... args) {
    int a[] = {
        (
            [&] {  // 这个 {} 包裹的是一个函数，lambda函数
                std::cout << args << std::endl;
            }() // 注意这里记得加上 () 表示直接执行这个函数，而不是只定义了一个lambda表达式

        , 0)...
    };
    for (int i = 0; i < sizeof...(args); ++i) {
        std::cout << a[i] << std::endl;
    }
}
```

这样做的一个问题在于，会导致一个栈空间的浪费，就是这个`a[]`，看看以后能不能委员会有更好的优化。



# 左右值引用

```cpp
int a = 100;
Student stu = Student(100);
```

上面这个例子中，包括平时写代码的时候，对 `xxx = xxx` 这种格式已经习以为常了，左值右值就是表示等号左右两边的东西。但是有一个特点是，左值即可以当作左值，也可以当成右值，但是右值只能是右值。比如：

```cpp
int 100 = a;  // 100只能当做右值，不能当成左值使用
int b = a; // a即可以是左值，又可以是右值
```

左右值的概念是搞清楚了，右值还有一个特点是将亡值。那么至于拷贝构造，移动构造，拷贝赋值，移动赋值这几个概念该如何辨析呢？

-   构造与赋值运算符在于，声明变量的时候就是调用构造函数，声明完变量之后，再进行`=`才是赋值运算。
-   拷贝与移动，拷贝往往对每个进行拷贝，传入是左引用，移动的话，一般传入右引用

下面给出一个非常强有力的工具，`std::move`的定义，它可以把数据进行移动而不是拷贝，当然原来的数据也就没有了。

```cpp
# define _GLIBCXX_NODISCARD [[__nodiscard__]]
template<typename _Tp>
_GLIBCXX_NODISCARD
constexpr typename std::remove_reference<_Tp>::type&&
   move(_Tp&& __t) noexcept{ 
    return static_cast<typename std::remove_reference<_Tp>::type&&>(__t); 
}

template<typename _Tp>
struct remove_reference
{ using type = __remove_reference(_Tp); };
```



# 转发函数

转发函数的作用非常简单，就是将数据按照原样转发即可，其原始定义如下

```cpp
template<typename _Tp>
[[nodiscard]]
constexpr _Tp&&
forward(typename std::remove_reference<_Tp>::type& __t) noexcept
{ return static_cast<_Tp&&>(__t); }

// 以及

template<typename _Tp>
[[nodiscard]]
constexpr _Tp&&
forward(typename std::remove_reference<_Tp>::type&& __t) noexcept
{
  static_assert(!std::is_lvalue_reference<_Tp>::value,
  "std::forward must not be used to convert an rvalue to an lvalue");
  return static_cast<_Tp&&>(__t);
}
```

通过以下例子来感受一下：

```cpp
#include <iostream>
using namespace std;
void fun(int &a) {
    cout << "左值引用函数fun1: " << a << endl;
}
void fun(const int& a) {
    cout << "常量左值引用函数fun2: " << a << endl;
}
void fun(int&& a) {
    cout << "右值引用函数fun3: " << a << endl;
}

int main() {
    int a = 1;
    const int b = 2;
    int c = 1ll;
    fun(a);
    fun(b);
    fun(c);
    fun(1);
    cout << "----------------" << endl;
    fun(forward<int>(a));
    // fun(forward<int>(b));  // error: 不能将const int转换为int
    fun(forward<int>(c));
    fun(forward<int>(1));
    return 0;
}
/**
输出结果如下：
左值引用函数fun1: 1
常量左值引用函数fun2: 2
左值引用函数fun1: 1
右值引用函数fun3: 1
----------------
右值引用函数fun3: 1
右值引用函数fun3: 1
右值引用函数fun3: 1
*/
```

# placement new

常用的new应该是如下：

```cpp
T *a = new T(args...);
// 但其实上面应该叫做constructor new，创造一个对象，并返回地址，还可以给定一个地址来创造对象，如下
auto ptr = static_cast<T *>(malloc(sizeof(T)));
new(ptr) T(args...);  // 已经有一块内存块了，在ptr指向处
```



# Trait

Traits（特性）是一个非常强大的工具，允许我们在编译中检查类型是否满足某些条件，根据这些条件可以对代码进行优化或者生成相应代码等。

Trait是一个类，通常会继承自`std::true_type`或者`std::false_type`，用来描述一个特性。

```cpp
using true_type =  integral_constant<bool, true>;
using false_type = integral_constant<bool, false>;
template<typename _Tp, _Tp __v>
struct integral_constant
{
  static constexpr _Tp                  value = __v;
  typedef _Tp                           value_type;
  typedef integral_constant<_Tp, __v>   type;
  constexpr operator value_type() const noexcept { return value; }  // 相当于重载bool了
  constexpr value_type operator()() const noexcept { return value; } // 相当于重载()了
};
```

举一个小例子

```cpp
template<class T> struct IsInteger : std::false_type{};	// 对于一般类型
template<> struct IsInteger<int> : std::true_type{};    // 特别处理 int 类型
```

接下来，将使用这个Trait

1.   直接判断

     >   ```cpp
     >   template<class T>
     >   void foo(T t){
     >       if constexpr (IsInteger<T>::value) {
     >           // 如果是int型，才执行这里的代码，并且这种判断是在编译过程判断的，所以不会影响性能
     >       }
     >   }
     >   ```

2.   静态断言

     >   ```cpp
     >   static_assert(IsInteger<int>::value, "不是整数");
     >   ```

3.   类型适配器

     >   ```cpp
     >   template<class T>
     >   struct IntegerAdapter: T {
     >       using type = T;
     >   };
     >   ```

4.   概念约束

     >   ```cpp
     >   template<class T>
     >   requires(IsInteger<T>())
     >   void bar(T t) {
     >       // 整数才能执行这个
     >   }
     >   ```
     >
     >   





















































