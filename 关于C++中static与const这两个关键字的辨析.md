---
title: 关于C++中static与const这两个关键字的辨析
id: 62
date: 2024-12-03 17:09:52
auther: yutanbird
cover: 
excerpt: 辨析以下const与static的区别
permalink: /archives/staticandconst
categories:
 - program
tags: 
 - c
---

# 介绍
const表示常量，属于只读"变量"（其实是常量），生命周期是局部型。
static表示静态变量，属于可读可写变量，生命周期是全局型（不考虑编译器优化）。
显然，两者属性是不一样的

# 类中
下面代码中
- `static int`: 在类中只有声明，并没有为其分配空间，所以static只能在类外初始化，并且是该块内进行初始化（不太好理解，相当于可以访问的域中）。
- `const int`: 是声明加定义，有空间，可以类内初始化，但是构造函数中初始化会覆盖掉原来类内初始化。并且构造函数中只能用“冒号”的方式进行初始化，因为const类型的是不可修改的值。
- `static const int`: const是声明加定义，所以有空间，可以在类内初始化；还有就是，在类中直接初始化的必须是const。

```c++
class A{
private:
	const int ci;
	static int si;
	static const int sci; // 与 const static int是一样的
public:
	A():ci(1){
		// ci = 10; // 在这里初始化是报错的，只能用上一行的初始化方法
	}
};
// 正确初始化static型变量
int A::si = 100;
const int A::sci = 200;
int main(){
	// int A::si = 100; // 报错
	// const int A::sci = 200; // 报错
	return 0;
}

```
# 表格
|                            | static     | const      | static const |
| -------------------------- | ---------- | ---------- | ------------ |
| 类内初始化                 | 不可以     | 可以       | 可以         |
| 生命周期                   | 全局型     | 局部型     | 局部型       |
| 定义与声明                 | 分离       | 不分       | 不分         |
| 该类所有对象的值           | 相同但可变 | 不同但不变 | 相同且不变   |
| 访问方式（公有情况下推荐） | 类::变量   | 对象.变量  | 类::变量     |
