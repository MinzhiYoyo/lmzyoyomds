---
title: C++学习
id: 56
date: 2024-12-03 17:09:51
auther: yutanbird
cover: 
excerpt: 553 C++复试
permalink: /archives/c%E5%AD%A6%E4%B9%A0
categories:
 - kaoyan
 - program
tags: 
 - c
 - 考研
---

# 类与抽象

## 抽象函数

&emsp;类中成员函数前面，添加关键字`virtual`，表明该函数是 **虚函数**，如果再在后面添加`=0`，就表示该函数为 **纯虚函数**。

- 虚函数：

  > 虚函数：子类 **可以不** 实现该函数，如`virtual int size(){ return this.size}`
  >
  > 纯虚函数：子类 **必须** 实现该函数，如`virtual int size() = 0;`
  >
  > 超类的构造函数不能为虚函数

## 类层次

执行顺序：超类的构造函数 ==> 子类的构造函数 ==> 子类的析构函数 ==> 超类的析构函数

子类实现了虚函数，就执行子类的的虚函数，否则就执行超类的虚函数。

- 那么考虑到一个问题，就是超类中有方法体的函数加不加`virtual`好像没什么区别

  > 加`virtual`是动态联编，不加`virtual`是静态联编
  >
  > 如果父类指针指向子类对象，加`virtual` 调用子类方法，不加`virtual`调用父类方法	
  >
  > `virtual`具有继承性，也就是说，子类中不声明`virtual`，子类的子类仍然是认为`virtual`

- 父指针指向子类对象实例`Father *ptr = new Son();`并且执行`ptr->fun();`

  > 如果超类中的`fun()`是纯虚函数，那么就执行子类中的函数
  >
  > 如果是虚函数或者普通成员函数，那么就执行超类中的
  >
  > 并且，子类的析构函数将不会执行

- 子指针指向子类对象实例`Son *ptr = new Son();`并且执行`ptr->fun();`

  > 一定执行子类中的函数，除非是子类中没有实现该函数，才会执行超类中的函数

- 内嵌对象调用顺序`class A{B b; C c;};`

  > b构造 => c构造 => a构造 => a析构 => c析构 => b析构

## 抽象类与接口

&emsp;C++没有接口的概念，但是Java中有接口的概念，使用的是关键词`interface`定义的。接口中所有的函数都是抽象了，抽象类中只需要包含至少一个抽象函数即可。Java中的抽象函数与C++中的纯虚函数是一样的，没有方法体。

## 函数对象

&emsp;如果重载`()`运算符，那么就可以像调用函数一样，调用类的对象实例。

```c++
template<typename T>  // 使用了函数模板
class lessthan{
private:
	const T val;    
public:
    lessthan(const T&val): val(v){}
    bool operator()(const T&x) const {return x<val;}
};

...
    
lessthan lti(42);
bool res = lti(82);  // 比较 82<42 
```

&emsp;补充一个知识点，[lambda](https://lmzyoyo.top/archives/cwithlambda)表达式也是生成了一个函数对象

- 用途

```c++
// 对容器c（C类）的所有对象都执行Oper操作
template<class C, class Oper>
void for_all(C& c, Oper op){
    for(auto &x:c)
        op(*x);
}

// 并且可以使用Lambda来调用for_all函数
vector<int> vel = {1,2,3,4};
for_all(vel, [](Add &a){a.add(2);}); 

// 就是对vel所有的元素都加2
// vel = {3,4,5,6};
```



# 标准库介绍

## std

`std::`包含如下库：`algorithm`, `array`, `chrono`, `cmath`, `complex`, `fstream`, `future`, `iostream`, `map`, `memory`, `random`, `regex`, `string`, `set`, `sstream`, `thread`, `unordered_map`, `utility`, `vector`。

# 多线程

&emsp;C++提供了多线程的标准库

```c++

```



# 智能指针



# 数学库

## 随机数

### C++随机数
&emsp;随机数生成器包括两个东西：**引擎** 和 **分布**，其中，引擎指的是以什么方法生成随机数，常见的是中间数平方法等。分布是指，生成的随机数映射成什么分布，如均匀分布，指数分布，正太分布等。

```c++
#include <iostream>
#include <random> // 随机数生成器标准库

int main() {
	std::default_random_engine dre;  // 设置为默认的引擎
	std::normal_distribution<> nd{2.4, 8};  // 设置分布为正太分布
	for (int _ = 0; _ < 10; _++) { // 生成10个符合正太分布的随机数
		cout << nd(dre) << ' ';
	}
	cout << endl;
	return 0;
}
```

### C++随机数引擎
- 伪随机数生成器（类模板）
  > linear_congruential_engine: 线性同余随机数引擎 
  > mersenne_twister_engine: Mersenne twister 随机数引擎
  > subtract_with_carry_engine: 带进位减法随机数引擎
- 适配器（类模板）
	> discard_block_engine	丢弃块随机数引擎适配器 
	> independent_bits_engine	独立位随机数引擎适配器 
	> shuffle_order_engine	Shuffle-order 随机数引擎适配器
- 实例化（类）我们一般直接使用下列的
	> **default_random_engine**	默认随机数引擎（常用）
	> **minstd_rand**	最小标准 minstd_rand 生成器
	> **minstd_rand0**	最小标准 minstd_rand0 生成器 
	> **mt19937**	Mersenne Twister 19937 生成器 (常用)
	> **mt19937_64**	Mersenne Twister 19937 生成器 (64 bit) 
	> **ranlux24_base**	Ranlux 24 基础生成器
	> **ranlux48_base**	Ranlux 48 基础生成器
	> 	**ranlux24**	Ranlux 24 生成器 
	> 	**ranlux48**	Ranlux 48 生成器 
	> 	**knuth_b**	Knuth-B 生成器
	> 	**random_device**	真随机数生成器

### C++随机数分布
**联合分布:**
uniform_int_distribution	Uniform discrete distribution (class template)
uniform_real_distribution	Uniform real distribution (class template)

**伯努利分布：**
bernoulli_distribution	Bernoulli distribution (class)
binomial_distribution	Binomial distribution (class template)
geometric_distribution	Geometric distribution (class template)
negative_binomial_distribution	Negative binomial distribution (class template)

**基于速率的分布**
poisson_distribution	Poisson distribution (class template)
exponential_distribution	Exponential distribution (class template)
gamma_distribution	Gamma distribution (class template)
weibull_distribution	Weibull distribution (class template)
extreme_value_distribution	Extreme Value distribution (class template)

**正太分布**
normal_distribution	Normal distribution (class template)
lognormal_distribution	Lognormal distribution (class template)
chi_squared_distribution	Chi-squared distribution (class template)
cauchy_distribution	Cauchy distribution (class template)
fisher_f_distribution	Fisher F-distribution (class template)
student_t_distribution	Student T-Distribution (class template)

**分段分布**
discrete_distribution	Discrete distribution (class template)
piecewise_constant_distribution	Piecewise constant distribution (class template)
piecewise_linear_distribution	Piecewise linear distribution (class template)


### C语言随机数
&emsp;除此之外，还有另外一个随机数生成器，这应该是为了兼容C语言而作保留的。

```c++
#include <stdlib.h>
#include <iostream>
#include <time.h>

int main(){
	srand((unsigned)time(NULL));   // 以时间作为随机数种子
    for(int _ = 0 ; _ < 10 ; _++){
        cout << rand()%(10-1)+1 << ' '; // 生成从1到10的随机数
    }
    cout << endl;
    return 0;
}

```

# 模板

## 可变参数模板

```c++
void pr() {}
template <typename T, typename... Tail>
void pr(T head, Tail... tails) {
	cout << head << ' ';
	pr(tails...);
}

pr(1, 2.2, "hello world", 41.2);
// 依次打印
// 执行顺序为：
// pr(1, 2.2, "hello world", 41.2)
// pr(2.2, "hello world", 41.2)
// pr("hello world", 41.2)
// pr(41.2)
// pr()
```

# 枚举
`enum`与`enum class`注意就行了，建议直接写`enum class`就不会出错
```c++
#include <iostream>

using namespace std;

class Enumclass {
public:
	enum class Temp {
		RED, BLUE
	};
	enum class Color {
		BLUE, RED
	};
	Temp t;
	Color c;
};



int main() {
	Enumclass::Temp::RED;
	Enumclass tc;
	tc.t = Enumclass::Temp::BLUE;
	tc.c = Enumclass::Color::BLUE;
	// tc.t = Enumclass::Color::BLUE; // 不是enum class就报错，是enum，且携程Enum::BLUE就不报错
	return 0;
}

```

# 小笔记

## 类成员函数后加`const`

&emsp;代表该函数不会修改类成员变量，也就是该函数对成员变量是 **只读** 的

```c++
class Student{
private:
    int age;
public:
    Student(int a=1):age(a){}
    int getAge() const{
        this->age++; // 报错
        return this->age;
    }
};
```



## `const`与`constexpr`

>`constexpr`：编译时求值
>
>`const`：在当前作用域内，其值不会发生改变

## `const `与指针

> `const int* p1`：表示`p1`所指内容为常量
>
> `int * const p2`：表示`p2`为常指针
>
> `int const * p3`：表示`p3`为指向常量的指针，与第一个好像没有什么区别

## 左值引用与右值引用

- 左值引用只能绑定到一个左值上面（如变量）。
- 右值引用可以绑定到一个右值（如函数返回值），临时变量，也不能绑定到左值，为了修改该临时变量，并且以后基本不会用到的变量
- const左值引用可以绑定到一个左值（如变量），临时变量上，不可修改临时变量。

## `explicit`用法

&emsp;`explicit`用在构造函数前面，使得该类对象初始化只能够显示的初始化，不能够隐式的初始化。

```c++
#include <iostream>
#include <string>
using namespace std;

class Book {
private:
    int price;
public:
    Book(int a) :price(a) {}
};

class Book2 {
private:
    int price;
public:
    explicit Book2(int a) :price(a) { }
};

int main() {
    Book a1 = Book(1);
    Book a2 = { 1 };
    Book a3(1);
    Book a4 = 1;
    Book a5{ 1 };

    Book2 b1 = Book2(1);
    Book2 b2 = { 1 }; // 错误
    Book2 b3(1);
    Book2 b4 = 1;  // 错误
    Book2 b5{ 1 };
	return 0;
}
```

## 类继承时的权限

```c++
class People{
private:
    ...
protected:
    ...
public:
    ...
};

class Student: public People{
    .....
};

class ClassMate: public Student{
  .....  
};
```

&emsp;其中，类里面的叫做类成员权限，冒号后面的叫做继承权限

- 如果`People`的类成员权限是：

|           | Student类及其子类 |   外部   |   友元   |
| :-------: | :---------------: | :------: | :------: |
|  private  |     不能访问      | 不能访问 | 可以访问 |
| protected |     可以访问      | 不能访问 | 可以访问 |
|  public   |     可以访问      | 可以访问 | 可以访问 |

- 如果`Student`的继承权限是：

  > `public`：`Student`的子类对`People`的所有成员保持原来的类成员权限
  >
  > `protected`：`People`的 **公有** 成员和 **保护** 成员在`Student`中变成 **保护** 的
  >
  > `privated`：`People`的 **公有** 成员和 **保护** 成员在`Student`中变成 **私有** 的

## 内联函数

&emsp;C++使用`inline`关键词 **定义** 内联函数，注意不是声明。对于类成员函数，编译器会自动进行内联（如果函数实现的功能可以内联的话）。内联函数可以作为取代宏定义的存在，这是因为其高效性能。编译器处理内联函数的时候，会把内联函数进行拷贝到调用它的地方，因此会产生代码膨胀的缺点。

&emsp;一下情况不用内联函数会更加好

- 函数体内代码比较长，一般超过十行的函数不要使用内联函数
- 函数体内出现循环或者其他复杂的控制结构，引起的开销会比较大