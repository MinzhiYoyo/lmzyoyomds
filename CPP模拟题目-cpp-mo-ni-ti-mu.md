---
title: CPP模拟题目
date: 2023-04-04 19:37:30.863
updated: 2024-02-27 15:34:56.38
url: /archives/cpp-mo-ni-ti-mu
categories: 
- 考研
tags: 
---

# 前沿

转载需要注明出处，本文不得用于售卖，任何人均可免费获取。

> 如果您觉得本文帮到了您，可以点击上方[支持](https://lmzyoyo.top/s/qing-wo-he-bei-nai-cha)按钮或者文末[打赏](https://lmzyoyo.top/s/qing-wo-he-bei-nai-cha)按钮来打赏作者。
> **ps：** 打赏行为与打赏金额请自愿，不影响使用本文来复习，且已有的资料全部都在博客上公开。
> 您的**支持**是**博客**前进的动力，仅此而已！！！

# 一，读程序

&emsp;阅读下列程序，写出答案

```cpp
// 1
#include <iostream>
using namespace std;

void function(char *& s1, char *& s2){
    int i = 0;
    for(; *s1 != *s2; s1++, s2++) i++;
    *(s1-1) = '\0';
    *(s2-1) = '\0';
    cout << i << endl;
}

int main(){
    char str1[] = "Hello Nanjing";
    char str2[] = "I am liang";
    char *p1 = str1, *p2 = str2;
    function(p1, p2);
    cout << p1 << endl;
    cout << p2 << endl;
    return 0;
}


```

```cpp
// 2.
#include <iostream>
using namespace std;
class Person{
public:
    Person(){num++;}
    ~Person(){num--;}
    static int num;
};
int Person::num = 0;

class Baby:public Person{
public:
    Baby(){num++;}
    ~Baby(){num--;}
    static int num;
}
int Baby::num = 0;

int num = 100;

Person p1;

int main(){
    int num = 0;
    cout << num << "," << ::num << "," << Person::num << "," << Baby::num << endl;
    {
        Person p2;
        Baby b1;
        cout << Person::num << "," << Baby::num << endl;
    }
    cout << Person::num << "," << Baby::num << endl;
    return 0;
}
```

```cpp
// 3.
#include <iostream>
using namespace std;

int main(){
    int n = 14;
    for(int  = 0; i < n; i++){
        if(i%6==0) continue;
        cout << i << ' ';
        if(i==6) break;
    }
    return 0;
}
```

```cpp
// 4.
#include <iostream>
#include <stdexcept>
using namespace std;
class Myerror: public runtime_error{
public:
    Myerror():runtime_error{"my error"}{
        
    }
};

int main(){
    try{
        int i = 10;
        if (i % 5 == 0){
            throw Myerror();
        }
    }catch(Myerror &err){
        cout << err.what() << endl;
    }
    return 0;
}

```

# 二、写程序

## 1、算法

&emsp;该题目选自力扣[[剑指 Offer II 100. 三角形中最小路径之和](https://leetcode.cn/problems/IlPe0q/)] <sup>[1]</sup>

```cpp
给定一个三角形 triangle ，找出自顶向下的最小路径和。

每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。

输入：triangle = [[2],[3,4],[6,5,7],[4,1,8,3]]
输出：11
解释：如下面简图所示：
   2
  3 4
 6 5 7
4 1 8 3
自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。
```

## 2、库

&emsp;请对我上面读程序的第2题进行扩写，请为一个人增加一些参数以及方法。同时要求重载部分运算操作符，请自行发挥。

# 三、程序填空

```cpp
/*
	题目要求：
		实现一个进制加密算法，比如 十进制954的十六进制3BA。
		我们对3BA进行加密，3BA -> 4CB -> BC4
		要求随机产生一个数字(0-2048)，并且对其进行加密，请输出加密前十六进制和加密后的字符串
		比如：
			产生随机数：954
			输出格式：
				3BA -> AC4
*/
#include <iostream>
#include <string>

【1】

string toHEX(int num){
	string s;
	if(num == 0){
		【5】
	}
	while(num != 0){
		int d = num % 16;
		char ch = '\0';
		if(【2】){
			ch = char(d + '0');
		}else{
			【3】
			ch = char(d + 'A');
		}
		s = ch + s;
		【4】
	}
	return s;
}

void encryption(char *p){
	if(【6】){
		return;
	}
	【7】
	if(*p <= 'Z' && *p >= 'A')
		cout << char(【8】);
	else cout << char(【9】);
}

int main(){
	int num = rand()%(2049);
	string src = toHEX(954);
	const char *str = src.c_str();
	int len = src.length();
	cout << str << " -> ";
	encryption(【10】);
	return 0;
}
```



# 引用

[1] 剑指 Offer II 100. 三角形中最小路径之和, https://leetcode.cn/problems/IlPe0q/

