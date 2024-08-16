---
title: 2023年东网CPP题目回忆版本
date: 2023-07-28 19:37:30.863
updated: 2024-03-21 23:30:00.129
url: /archives/kaoyancpp2023
categories: 
- 考研
tags: 
---

# 版权声明

&emsp;本文由考生**回忆**，部分细节记不太清，仅作为参考。除此之外，还有一份[模拟题](https://lmzyoyo.top/archives/cpp-mo-ni-ti-mu)。

&emsp;***切记不要乱传本文，以免对作者造成不必要的麻烦，作者写本文无任何盈利行为，请相互体谅！！！***

&emsp;严禁将本文用于商业用途，本文不得以任何方式售卖或绑定售卖。如果您获取本文时花费了金钱，与作者无关。

> 如果您觉得本文帮到了您，可以点击上方[支持](https://lmzyoyo.top/s/qing-wo-he-bei-nai-cha)按钮或者文末[打赏](https://lmzyoyo.top/s/qing-wo-he-bei-nai-cha)按钮来打赏作者。
> **ps：** 打赏行为与打赏金额请自愿，不影响使用本文来复习，且已有的资料全部都在博客上公开。
> 您的**支持**是**博客**前进的动力，仅此而已！！！

&emsp;***禁止培训机构使用本文盈利***。

# 第一题 阅读程序，写出结果

## 1、

```cpp
#include <iostream>

using namespace std;


void fun1(char *s1, char *s2){
	int i = 0;
	for(; *s1 == *s2; s1++, s2++){
		i++;
	}
	cout << s1 << endl;
	cout << i << endl;
}

void fun2(char *& s1, char *& s2){
	int i = 0;
	for(; *s1 == *s2; s1++, s2++){
		i++;
	}
	*(s1-1) = '\0';
	*(s2-1) = '\0';
}

int main(){
	char string1[] = "I love Nanjing";
	char string2[] = "I love Southeast University";
	char *p1 = string1;
	char *p2 = string2;
	fun1(p1, p2);
	fun2(p1, p2);
	cout << p1 << endl;
	cout << p2 << endl;
	return 0;
}

```

## 2、

***注***：本题细节地方记不清了。

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student{
public:
	Student(){
		num++;
	}	
	~Student(){num--;}
	static int num;
private:
	string name;
};
int Student::num = 0;

class Undergraduate: public Student{
public:
	Undergraduate(int i = 0, float s = 100){
		id = i;
		score = s;
		num++;
	}
	~Undergraduate(){num--;}
	static int num;
private:
	int id;
	float score;
};
int Undergraduate::num = 0;

class Postgraduate: public Student{
public:
	Postgraduate(string s = "UNDK", string n = "KDS"){
		sa = s;
		na = n;
		num++;
	}
	~Postgraduate(){num--;}
	static int num;
private:
	string sa, na;
};
int Postgraduate::num = 0;

int num = 100;
Undergraduate ug(1);
Postgraduate p;
int main(){
	int num = 0;
    // 这里所有的输出都简写了，因为记不得，其实原题是有部分英文的
    // 诸如 "There are" << num << "students" 之类的，不影响考点
	cout << num << endl;   
	cout << ::num << endl;
	cout << ug.num << endl;
	cout << p.num << endl;
	cout << Student::num << endl;
	{
		Undergraduate u1;
		Postgraduate p1;
		cout << u1.num  << endl;
		cout << p1.num << endl;
		cout << Student::num << endl;
	}
	Undergraduate *u = new Undergraduate;
	cout << Undergraduate::num << endl;
	// cout << Postgraduate::num << endl;
	cout << Student::num << endl;
	delete u;
	cout << Undergraduate::num << endl;
	// cout << Postgraduate::num << endl;
	cout << Student::num << endl;
}

```



## 3、

```cpp
#include <iostream>

using namespace std;

class Test{
public:
	Test(){
		num++;
	}
	~Test(){num--;}
	static void print(){
		cout << "T count: " << num << endl;
	}
private:
	static int num;
};
int Test::num = 0;

void fun(Test *p){
	Test m3;
	p = new Test[5];
	p->print();
	delete[] p;
	p = nullptr;
}

Test t1;
int main(){
	t1.print();
	Test t2;
	Test *ptr = nullptr;
	ptr->print();
	fun(ptr);
	ptr = new Test;
	ptr->print();
	delete ptr;
	Test::print();
	return 0;
}


```



## 4、

```cpp
#include <iostream>

using namespace std;

int main(){
	int n = 13;
	for(int i = 0 ;i < n;i++){
		if(i%5==0) continue;
		cout << i << ' ';
		if(i%2==0) cout << endl;
		if(i%10==0) break;
	}
}

```



## 5、

```cpp
#include <iostream>
#include <stdexcept>

using namespace std;


class ErrorA: public runtime_error{
public:
	ErrorA():runtime_error{"errorA"}{

	}
};

class ErrorB: public runtime_error{
public:
	ErrorB():runtime_error{"errorB"}{

	}
};

class ErrorC: public ErrorA{
public:
	ErrorC(){
		runtime_error{"errorC"};
	}
};

int main(){
	for(int i = 0 ;i < 4; i++){
		try{
			switch(i){
				case 0: throw runtime_error{"runtime_error"}; break;
				case 1: throw ErrorA();
				case 2: throw ErrorB();
				case 3: throw ErrorC();
			}
		}catch(ErrorA &err){
			cout << err.what() << endl;
		}catch(ErrorB &err){
			cout << err.what() << endl;
		}catch(ErrorC &err){
			cout << err.what() << endl;
		}catch(runtime_error &err){
			cout << err.what() << endl;
		}

	}

	return 0;
}


```

---

# 第二题 程序填空

## 1、随机数排序

&emsp;产生一个随机长度的随机数数组，并使用冒泡排序对数组进行排序，然后以16进制的形式输出数组。（***注***：题目原话记不得了）

&emsp;在测试中，如果不使用`srand((unsigned)time(NULL));`作为随机种子，每次的结构都是一样的，所以我加了两行注释，原题是没有的。

```cpp
#include <iostream>
#include <string>
// #include <ctime>  // 这一行是自己加的，原题没有
______【1】___________ // 设空

void genterator(int *arr, int size){
	for(int i = 0 ;i < size; i++){
		________【2】_________ // 设空
	}
}

void bubble(int *arr, int size){
	for(int i = 0; i < size; i++){
		bool flag = false;
		for(int j = 0 ; j < _____【3】____; j++){ // size-1-i设空
			if(arr[j] > arr[j+1]){
				int temp = arr[j];
				arr[j] = arr[j+1];
				arr[j+1] = temp;
				flag = true;
			}
		}
        ______【4】______ // 设空
	}
}

string transform(int num){
	string s;
	if(num == 0)
		______【5】_______ // 设空	
	while(num != 0){
		int d = num % 16;
		if(___【6】___){ // 设空
			s = char(d + '0') + s;
		}else{
			___【7】____ // 空
			s = char(d + 'A') + s;
		}
		_____【8】_____ // 空
	}
	return s;
}

int main(){
	// srand((unsigned)time(NULL));  // 这一行是自己加的，原题没有
	_______【9】_______ // 空
	int a[size];
	genterator(a, size);
	bubble(a, size);
	for(int i = 0 ;i < size; i++){
		cout << transform(a[i]) << endl;
	}
	return 0;
}

```



## 2、逆序输出

&emsp;逆序输出前若干个非空白字符，要求下列程序能够输出：`rebyCymsisihtolleH`。

***注***：本题题目原话忘记，只记得本题要求能够输出`rebyCymsisihtolleH`字符串即可。

```cpp
#include <iostream>

________【1】_________ // 空
________【2】_______ // 空

int main(){
	char str[] = "Hello this is my Cyber S&E!!";
	_______【3】__________ // 空
	_______【4】__________ // 空
	return 0;
}

void reverString(char *a, _【5】_){ // b设空
	static int chars = 0;
	if(______【6】_____)  // 空
		return ;
	
	if(a[b] != ' ')
		____【7】___ // 空
	reverString(a, ___【8】__); // b+1设空
	if(__【9】___) // 空
		cout << a[b];
}
```



---

# 第三题 写程序

## 1、最长路径问题（偏算法）

&emsp;输入一个数字n表示层数，在输入数字来表示三角形，要求三角形求解从顶到低的最长路径，在力扣中有类似的题目，也可以做做这道题[leetcode120](https://leetcode.cn/problems/triangle/)。（***注***：原题描述记不清；原题数据忘记，本题数据是作者编的）。

```
      7
	 4 2
	1 6 0
   2 4 7 5
  2 4 6 7 5
```

&emsp;从第一层的`7`出发，走到第五层，求出经过路径和最长的路径和。要求使用递归与递推两种方法，并且按照下面的输入与输出设计程序。从上一层向下一层走的时候，只能走两边的路。如第三层的`6`，那么下一层只能选择第四层的`4`或者`7`。

**输入：**

```
5
7 4 2 1 6 0 2 4 7 5 2 4 6 4 5
```

**输出：**

```
30
7
4 2
1 6 0
2 4 7 5
2 4 6 4 5


30
30
23 21
11 19 13
6 10 13 10
2 4 6 4 5
```

&emsp;注意输出的顺序哦！！！\^_\^

&emsp;下面是题目已给的代码：（细节地方记不清了）

```cpp
#include <iostream>

using namespace std;

void print(int *arr, int size);
int *input(int size);
int digui(int *arr, int size, int row, int col);
int ditui(int *arr, int size);

int main(){
    
    return 0;
}
```



## 2、猫狗问题（考察OOP）

&emsp;本题是完全记不清了，要求写几个类，题量非常大。题目会给你输出的结果，输出结果部分细节忘记。考察知识点也非常多，我也记不得了。

***总之这道题看看就好***。

&emsp;按照如下输出结果，设计三个类`Pet`、`Cat`、`Dog`。并且要求重载`<<`运算符，重载这里我没写出来，所以`main`函数里面我也忘了，各位自己想想怎么写吧。

```
===============coming===========
new pet,
dog:wangcai, new
new pet,
dog:dahuang, new
new pet,
cat:xiaomao, new
new pet,
cat:huhu, new
new pet,
cat: ~~
==============crying==========
Dog: wangcai, wawa!!
Dog: dahuang, wawa!!
Cat: xiaomao, miaomiao~~
Cat: huhu, miaomiao~~
~~
==============feed=============
copy pet,
Dog:wangcai, eat bone!!
leave dog, wangcai
deleted pet
=============leave============
leave dog, wangcai
deleted pet
leave dog, dahuang
deleted pet
leave cat,xiaomao
deleted pet
leave cat,huhu
deleted pet
leave cat,
```

&emsp;题目已给代码：

```cpp
/*
实现三个类，Pet Cat Dog，然后 还要实现重载 << 操作符，反正就是很大
*/
#include <iostream>
#include <cstring>

using namespace std;

void feed(Dog dog){
	dog.eat();
}

int main(){
	Pet *pets[5];
	cout << "===============coming===========" << endl;
    // 这一部分不记得是我自己写的还是题目有的
	pets[0] = new Dog("wangcai");
	pets[1] = new Dog("dahuang");
	pets[2] = new Cat("xiaomao");
	pets[3] = new Cat("huhu");
	pets[4] = new Cat(NULL);
	cout << "==============crying==========" << endl;
	for(int i = 0 ;i < 5; i++)
    {
        // 这里记不得了，这里是重载 << 的地方输出 crying
    }
	cout << "==============feed=============" << endl;
	feed(*dynamic_cast<Dog*>(pets[0]));
	cout << "=============leave============" << endl;
	for(int i = 0 ;i < 5; i++){
		delete pets[i];
	}
	return 0;
}

```

---

# 参考答案

## 第一题

### 1、

```
Nanjing
7
Nanjing
Southeast University
```

### 2、

```
0
100
1
1
2
2
2
4
2
3
1
2
```

### 3、

```
T count: 1
T count: 2
T count: 8
T count: 3
T count: 2
```

### 4、

```
1 2 
3 4 
6 
7 8 
9 11 12
```

### 5、

```
runtime_error
errorA
errorB
errorA
```

## 第二题

### 1、

```cpp
#include <iostream>
#include <string>
// #include <ctime>  // 这一行是自己加的，原题没有
using namespace std; // 设空

void genterator(int *arr, int size){
	for(int i = 0 ;i < size; i++){
		arr[i] = rand()%100; // 设空
	}
}

void bubble(int *arr, int size){
	for(int i = 0; i < size; i++){
		bool flag = false;
		for(int j = 0 ; j < size - 1 - i; j++){ // size-1-i设空
			if(arr[j] > arr[j+1]){
				int temp = arr[j];
				arr[j] = arr[j+1];
				arr[j+1] = temp;
				flag = true;
			}
		}
		if(!flag) return; // 设空
	}
}

string transform(int num){
	string s;
	if(num == 0)
		return string("0"); // 设空	
	while(num != 0){
		int d = num % 16;
		if(d < 10){ // 设空
			s = char(d + '0') + s;
		}else{
			d -= 10; // 空
			s = char(d + 'A') + s;
		}
		num /= 16; // 空
	}
	return s;
}

int main(){
	// srand((unsigned)time(NULL));  // 这一行是自己加的，原题没有
	const int size = rand()%20; // 空
	int a[size];
	genterator(a, size);
	bubble(a, size);
	for(int i = 0 ;i < size; i++){
		cout << transform(a[i]) << endl;
	}
	return 0;
}
```



### 2、

```cpp
#include <iostream>

using namespace std; // 空
void reverString(char *a, int b); // 空

int main(){
	char str[] = "Hello this is my Cyber S&E!!";
	cout << str << endl; // 空
	reverString(str, 0); // 空
	return 0;
}

void reverString(char *a, int b){ // b设空
	static int chars = 0;
	if(chars == 18 || a[b] == '\0')  // 空
		return ;
	
	if(a[b] != ' ')
		chars++; // 空
	reverString(a, b+1); // b+1设空
	if(a[b] != ' ') // 空
		cout << a[b];
}
```

## 第三题

***答案是自己写的，仅供参考***

### 1、

```cpp
#include <iostream>

using namespace std;

void print(int *arr, int size);
int *input(int size);
int digui(int *arr, int size, int row, int col);
int ditui(int *arr, int size);

int main(){
	int n;
	cin >> n;
	int* arr = input(n);
	cout << digui(arr, n, 0, 0) << endl;
	print(arr, n);
	cout << ditui(arr, n) << endl;
	print(arr, n);
	return 0;
}

void print(int *arr, int size){
	int index = 0;
	for(int n = 1; n <= size; n++){
		for(int i = 0 ; i < n ; i++){
			cout << arr[index++] << ' ';
		}
		cout << endl;
	}
}

int *input(int size){
	if(size <= 0) return nullptr;
	int ns = (size+1)*size / 2;
	int *ans = new int[ns];
	for(int i = 0 ;i < ns; i++)
		cin >> ans[i];
	return ans;
}

int digui(int *arr, int size, int row, int col){
	if(size == row) return 0;
	int l = digui(arr, size, row+1, col);
	int r = digui(arr, size, row+1, col+1);
	int index = row * (row+1)/2 + col;
	return l > r ? l + arr[index] : r + arr[index];
}

int ditui(int *arr, int size){
	int s = size * (size + 1) / 2 - 1;
	s -= size;
	for(int row = size - 2; row >= 0; row--){
		for(; row * (row + 1) / 2 <= s; s--){
			int l = arr[s+row+1];
			int r = arr[s+row+2];
			int m = l > r ? l : r;
			arr[s] += m;
		}
	}
	return arr[0];
}

```



### 2、部分正确参考答案

***本答案只有部分正确，重载运算符是错的，真的只能作为参考，反正希望各位能自己写出来吧***

```cpp
/*
实现三个类，Pet Cat Dog，然后 还要实现重载 << 操作符，反正就是很大
*/
#include <iostream>
#include <cstring>

using namespace std;


class Pet{
public:
	Pet(const char *p = NULL){
		copy(p);
		cout << "new pet," << endl;
	}
	Pet(const Pet &p){
		copy(p.name);
		cout << "copy pet," << endl;
	}
	// friend ostream & operator<<(ostream &out){
	// 	return out;
	// }
	virtual void eat(){}
	virtual ~Pet(){
		cout << "deleted pet" << endl;
		delete[] name;
	}
protected:
	void copy(const char *p){
		if(p){
			int len = strlen(p);
			name = new char[len+1];
			for(int i = 0 ;i < len ;i++)
				name[i] = p[i];
			name[len] = '\0';
		}
	}
	char *name;
};

class Dog:public Pet{
public:
	Dog(const char *p = NULL):Pet(p){
		cout << "dog:" << name << ", new" << endl; // 具体忘了
	}
	ostream &operator<<(ostream &out){
		out << "Dog:" << name << ", wawa!!" << endl;
		return out;
	}
	~Dog(){
		cout << "leave dog, " << name << endl;
	}
	void eat(){
		cout << "Dog:" << name << ", eat bone!!" << endl;
	}
};

class Cat:public Pet{
public:
	Cat(const char *p = NULL):Pet(p){
		if(p) cout << "cat:" << name << ", new" << endl;
		else cout << "cat: ~~" << endl;
	}
	friend ostream &operator<<(ostream &out, const Cat &cat){
		if(cat.name) cout << "Cat:" << cat.name << ", miaomiao~~" << endl;
		else cout << "~~" << endl;
		return out;
	}
	~Cat(){
		if(name){
			cout << "leave cat," << name << endl;
		}
	}
};

void feed(Dog dog){
	dog.eat();
}

int main(){
	Pet *pets[5];
	cout << "===============coming===========" << endl;
	pets[0] = new Dog("wangcai");
	pets[1] = new Dog("dahuang");
	pets[2] = new Cat("xiaomao");
	pets[3] = new Cat("huhu");
	pets[4] = new Cat(NULL);
	cout << "==============crying==========" << endl;
	for(int i = 0 ;i < 5; i++)
		// cout << *pets[i] << endl;
		{}
	cout << "==============feed=============" << endl;
	feed(*dynamic_cast<Dog*>(pets[0]));
	cout << "=============leave============" << endl;
	for(int i = 0 ;i < 5; i++){
		delete pets[i];
	}
	return 0;
}

```