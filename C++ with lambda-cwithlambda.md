---
title: C++ with lambda
date: 2022-05-18 20:41:06.0
updated: 2023-01-02 07:38:39.878
url: /archives/cwithlambda
categories: 
- 编程学习
tags: 
- C++
- λ
- lambda
---



# 什么是`λ`演算

  首先，这个`λ`演算是非常牛逼的一个东西，可以看一下[维基百科——λ](https://zh.wikipedia.org/wiki/%CE%9B%E6%BC%94%E7%AE%97)的介绍

# `C++` 适用 `λ` 表达式

  众所周知，如果一个工程中用了很多函数名，最终会导致命名污染。
	
  不妨假设如下场景，如果你有一个函数，需要重复写一段代码，你会选择将这段代码封装成另一个函数`FUN`。但是，一个函数就需要一个名字，你封装成的另一个函数`FUN`就需要一个名字，但是这个`FUN`函数，你在其他地方又用不到了，那么就可以尝试适用匿名函数来实现了。

## 简单用法

```c++
int function() {
	auto add = [](int a, int b){
        if(a % 2) return a+b;
        return a;
    };
	cout << add(3,4);
	return 0;
}

// 上面这个结果就会输出 7
// 用到这里你会发现lambda没有什么牛逼之处
// 但是，如果你不想这个add函数在外部用到，而仅仅是function函数中使用
// 那么它将带来很大的遍历，你不需要考虑如何去命名
```

## 细节用法

​	可能不太理解为什么像上述写法这么写 `[](){}`，这个为什么构成`lambda`表达式

- `[]`部分：这里是表示捕获部分，为什么需要捕获呢？

  > 因为`add`函数也许需要用到`function`函数中的东西。
  >
  > 有人发出疑问？不是可以传参吗？
  >
  > 但是，传参造成了很多不必要的麻烦，如：你需要先声明传参类型，然后你一般需要传引用，最后导致参数列表变得冗长
  >
  > 而`[]`很好的解决这个问题
  >
  > - `[]`：为空，那么就是不捕获任何参数，就是`lambda`表达式中不能使用外部的变量
  > - `[=]`：值捕获，并且捕获所有变量
  > - `[&]`：引用捕获，也是捕获所有变量
  > - `[this]`：捕获当前的类的指针
  > - `[a]`：只捕获`a`的值
  > - `[&a]`：捕获`a`的引用
  > - `[=, &a, &b]`：除了`a,b`引用传递外，其余的值传递
  > - `[&, a, b]`：除了`a,b`值传递外，其余的引用传递

- `()`部分：这里是参数列表，和普通函数一样，值传递和引用传递都可以

- `{}`部分：代码块

---

​	除此之外，还和普通函数一样，有说明符和返回值的约束。

```c++
// 说明符的介绍
auto f = [&]() mutable
{
    return 1;
}
```

- `mutable`：允许 函数体 修改各个复制捕获的对象，以及调用其非 `const` 成员函数；
- `constexpr`：显式指定函数调用运算符为 `constexpr`函数。此说明符不存在时，若函数调用运算符恰好满足针对 `constexpr`函数的所有要求，则它也会是 `constexpr`； (C++17 起)
- `consteval`：指定函数调用运算符为立即函数。不能同时使用 `consteval` 和 `constexpr`。(C++20 起)

```c++
// 返回值介绍
// 限制返回 int
auto f = [&]() -> int
{
    return 1;
}
```

## 补充说明

​	有人发现了，前面都是使用`auto`关键词声明的，那么可不可以声明为具体类型了，答案是***也可以***的。既然是匿名函数，肯定没有固定的`typeid`，经过测试，那怕完全相同的两个函数，其`typeid`都是***不一样***的。就像`int a = 1; int b = 1;`中`a,b`不是用一个东西，仅仅是两个变量的值一样，但是`a,b`的`typeid`是一样的。`auto f1 = [](){}; auto f2 = [](){};`中，`f1, f2`他们仅仅是地址对应的值相同而已，都是只执行了`[](){}`这个函数。

​	但是，前面说可以声明为具体类型，那么，该怎么实现呢？答案是：***函数指针***

- 注意，这里的返回值`-> int`不能省略
- 而且，如果下述实现，是有`typeid(f)==typeid(g)`
- 并且还有`typeid(f) == typeid(g) == typeid(h)`
- 至于这个为什么呢？我也没有想明白这些，之后再努力看看才行。

```c++
int (*f)() = []() -> int {return 1;};
int (*g)() = []() -> int {return 1;};

int a(){return 1;}
int (*h)() = a; // 也可写做 int(*h)() = &a; 因为函数名本来就是地址
```