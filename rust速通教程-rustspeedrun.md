---
title: rust速通教程
date: 2023-08-05 19:27:00.0
updated: 2023-08-08 22:25:12.025
url: /archives/rustspeedrun
categories: 
- 编程语言
tags: 
- rust
---

# 前言

&emsp;为什么要写rust语言呢？有句话说的好：go与Java、rust与C++，这句话是谁说的呢？没错，就是我说的。好了，回归正题，大概是平常学习中，耳濡目染地将C++与rust联系起来了，而我本人又是常用C++选手，所以，还是需要学一下rust。再加之，我前面刚刚写了[函数式编程-λ演算](https://lmzyoyo.top/archives/lambdacalculus)。

&emsp;阅读本教程前，需要读者有Java/C++/Python/JavaScript等编程语言的基础，因此，本教程应该是速通教程。

&emsp;rust是一门编译型语言，

- 重要链接：
    - [官网](https://www.rust-lang.org/)
    - [The Rust Programming Language](https://doc.rust-lang.org/book/)
    - 《Rust权威指南》，当然这个没有链接

# 第一章 安装编译与预学习

## 安装与编译

&emsp;查看官网[安装教程](https://www.rust-lang.org/tools/install)即可。

&emsp;下面是两种方式的编译，一种是单文件，一种是工程性。

```shell
rustc main.rs  # 单文件编译
./main.exe  # 运行
```

接下来是用cargo来完成项目的开发

```shell
cargo new <folder-name>  # 新建一个文件夹，文件夹里面是一个rust工程

# 假设新建hello工程
cargo new hello

cargo build  # 编译，以debug形式
./target/debug/hello.exe

# 上述两条命令可以替换成一条
cargo run

# 以release编译
cargo build --release

# 查看编译是否通过，不会运行，速度快一点
cargo check
```

`Cargo`的一些其他命令

```shell
# 在本地构建一份包含你所有依赖的文档，并且以浏览器打开
cargo doc --open 
```



&emsp;工程性的文件夹介绍

```shell
hello	--- 
		|
		|--- .git	# 这个是git文件夹
		|--- src	# 这个是存放源码的文件夹
		|--- target	# 这个是生成路径
		|--- .gitignore	# 这个是git忽略上传相关的文件
		|--- Cargo.lock	# 这个是工程版本信息的文件
		|--- Cargo.toml	# 这个是作者信息的文件，工程依赖信息等等许多东西
```

下面说一下`Cargo.toml`文件，假如我们需要依赖于其他包，如随机数`rand`包，我们就需要导入。

```toml
[dependencies]
# 这其实是^0.3.14的缩写，任何与0.3.14版本公共API兼容的版本
# 编译后注意看Cargo.lock文件的变化
rand = "0.3.14"
```



## 预学习

&emsp;接下来，是编程语言的第一步，打印“hello, world”。

```rust
// 下面是main函数，同C++一样，需要有一个main函数
/*
	这四行演示了注释应该是怎么写的
*/
fn main() {
    println!("Hello, world!");
}
```

&emsp;接下来，看一个例子，以便能够学到一些基本的知识。

```rust
// src/main.rs

use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main(){
    let secret_num = rand::thread_rng().gen_range(1, 101);
    println!(" ===== 猜数字游戏 ====== ");

    loop {
        println!("请输入你猜测的数字: ");
        let mut guess = String::new();
        io::stdin().read_line(&mut guess)
    		.expect("输入失败了");

        let guess_num: u32 = match guess.trim().parse(){
            Ok(num) => num,
            Err(err) => {
                println!("由于“{}”，转换失败啦", err);
                continue;
            }
        };
                    
        // 下面进行比较
        match guess_num.cmp(&secret_num) {
            Ordering::Equal => {
                println!("猜对了");
                println!("你猜测的数字是：{}生成的数字是：{}", guess, secret_num);
                break;
            }
            Ordering::Greater => println!("你猜的太大了"),
            Ordering::Less => println!("你猜的太小了"),
        }
    }
}
```

- rust语言会预导入常用的模块，如果用到的模块不在预导入内，需要手动使用use来导入
- `println!`其实是宏，而不是函数，而且会默认换行。并且不能用`println!(m);`直接打印m的值
- 注意函数`read_line()`的参数写成了`&mut guess`，其中的`&`表示传入引用，而`mut`表示可变，这里是传入可变引用。
- 注意`expect()`，`read_line()`函数还会返回一个`io::Result`，同JavaScript类似。`io::Result`是一个枚举类型，拥有`Ok`和`Err`两个变体。
- 关于这里的`rand`，需要在`Cargo.toml`里面预先导入`rand`包，才能`use rand::Rng;`。回到函数`rand::thread_rng()`生成一个本地线程的随机数生成器，并通过操作系统获得随机数种子。`gen_range(1, 101)`生成范围为：[1, 101) 的随机数。
- 剩下的流程控制，我们放到后面讲。

## 关键字

- `as` - 强制类型转换，消除特定包含项的 trait 的歧义，或者对 `use` 语句中的项重命名
- `async` - 返回一个 `Future` 而不是阻塞当前线程
- `await` - 暂停执行直到 `Future` 的结果就绪
- `break` - 立刻退出循环
- `const` - 定义常量或不变裸指针（constant raw pointer）
- `continue` - 继续进入下一次循环迭代
- `crate` - 在模块路径中，代指 crate root
- `dyn` - 动态分发 trait 对象
- `else` - 作为 `if` 和 `if let` 控制流结构的 fallback
- `enum` - 定义一个枚举
- `extern` - 链接一个外部函数或变量
- `false` - 布尔字面值 `false`
- `fn` - 定义一个函数或 **函数指针类型** (*function pointer type*)
- `for` - 遍历一个迭代器或实现一个 trait 或者指定一个更高级的生命周期
- `if` - 基于条件表达式的结果分支
- `impl` - 实现自有或 trait 功能
- `in` - `for` 循环语法的一部分
- `let` - 绑定一个变量
- `loop` - 无条件循环
- `match` - 模式匹配
- `mod` - 定义一个模块
- `move` - 使闭包获取其所捕获项的所有权
- `mut` - 表示引用、裸指针或模式绑定的可变性
- `pub` - 表示结构体字段、`impl` 块或模块的公有可见性
- `ref` - 通过引用绑定
- `return` - 从函数中返回
- `Self` - 定义或实现 trait 的类型的类型别名
- `self` - 表示方法本身或当前模块
- `static` - 表示全局变量或在整个程序执行期间保持其生命周期
- `struct` - 定义一个结构体
- `super` - 表示当前模块的父模块
- `trait` - 定义一个 trait
- `true` - 布尔字面值 `true`
- `type` - 定义一个类型别名或关联类型
- `union` - 定义一个 [union](https://doc.rust-lang.org/reference/items/unions.html) 并且是 union 声明中唯一用到的关键字
- `unsafe` - 表示不安全的代码、函数、trait 或实现
- `use` - 引入外部空间的符号
- `where` - 表示一个约束类型的从句
- `while` - 基于一个表达式的结果判断是否进行循环
- 保留关键字：
    - `abstract`，`become`，`box`，`do`，`final`，`macro`，`override`，`priv`，`try`，`typeof`，`unsized`，`virtual`，`yield`

---

# 第二章 数据类型与变量

## 数据类型

&emsp;数据类型分为[标量（scalar）类型](#标量类型)与[复合（compound）类型](#复合类型)，rust是一门静态类型语言，声明变量必须要知道是什么类型，但是仍然可以使用let关键字是因为编译器大多数情况下可以推断类型。如果推断不出来的地方，需要我们手动声明类型。

```rust
// 手动声明为u32类型，去掉‘: u32’会报错
let guess_num: u32 = "199".parse().expect("转换失败啦！！！");

// 还可以用下面方法声明类型
let i = 390u32;

// 位数比较多还可以用_来分隔
let j = 1000_000_000_000_000;
```

### 标量类型

- 整数类型：见下表，
    - 除了最后一行外其他都应该很容易明白。最后一行在不同架构机器上不一样，在64位机器上是64bit，在32位机器上是32bit。
    - 关于其他进制的写法：
        - 十进制：正常写，如`98_2223`
        - 十六进制：加`0x`，如`0xff`
        - 八进制：加`0o`，如`0o77`
        - 二进制：加`0b`，如`0b1111_0000`
        - Byte（只能是u8类型）：加`b''`，如`b'A'`
    - 整数溢出问题，在debug模式下会检测代码，并且触发程序的panic，并且标记出来。在release模式下，不会触发，溢出就轮转同其他语言一样。

|  长度  | 有符号 | 无符号 |
| :----: | :----: | :----: |
| 8-bit  |   i8   |   u8   |
| 16-bit |  i16   |  u16   |
| 32-bit |  i32   |  u32   |
| 64-bit |  i64   |  u64   |
|  arch  | isize  | usize  |

- 浮点数类型：
    - 浮点数只有两种基础的浮点数：`f32`和`f64`
    - 如果没有明确声明为f32，默认就是f64，在现在的机器上，两者运行速度相差无几。
    - 浮点类型以IEEE-754标准进行表述，对应标准里的单精度与双精度。
- 字符类型：
    - 占用4字节，是一个Unicode标量值，跨度是：[U+0000, U+D7FF]∪[U+E000, U+10FFFF]

### 复合类型

rust提供了两种最基础的符合类型

- 元组（tuple）类型：长度固定，类型可多种，索引从0开始，索引方式为`name.数字`，使用圆括号`()`

```rust
let tup = (13, "dh", 32.9);
tup.0; // 13
tup.1; // "dh"
tup.2; // 32.9
```



- 数组（array）类型：长度固定，相同类型，索引从0开始，索引方式是`name[数字]`，使用方括号`[]`。注意区别于`vector`类型

```rust
let a = [1, 2, 3, 4, 5]; // [1, 2, 3, 4, 5];
let b = [3;5]; // [3, 3, 3, 3, 3]
a[0]; // 1
a[1]; // 2
```



## 变量

### 可变性

&emsp;rust的变量默认是 **不可变(immutable)** 的，因此，如果要变量可变，需要提前声明，使用关键字`mut`。但是，千万不要把变量不变性和常量混为一谈。

```rust
let mut i = 10;
// let i = 10; // 这是错的，因为后面重新对i赋值了

i = 100;

let MAX_VAULE = 1000; // 不可变的变量
const MAX = 1000; // 常量
```

值得一提的是，`const`声明的变量是编译时求值得常量，类似于C++的`constexpr`。而不可变的变量类似与C++的`const`。

### 变量的覆盖

```rust
fn main(){
    let x = 5;
    let x = x+1;
    let x = x*2;
    println!("x的值：{}", x);
}
```

&emsp;上述例子中，首先代码不会报错且能够运行正常，可能立马就有疑问了，x不是不可变的变量吗？要与[可变性](#可变性)中的例子区分开，这里再次赋值的时候多了`let`，意思是将前一个变量覆盖掉。再看下面两个例子。

```rust
fn main(){
    // 第一个例子
    let li1 = "hello";
    let li1 = li1.len();
    
    // 第二个例子
    let mut li2 = "hello";
    li2 = li2.len();
}
```

上述代码中，例子一正常运行，例子二会报错。这是因为，例子二中变量可变，但是不允许更改类型。

# 第三章 函数

## 基本介绍

&emsp;lrust代码推荐使用蛇形命名法（snake case）来规范函数和变量名称，用小写字母命名，使用下划线`_`分隔。

```rust
fn main(){
    println!("主函数中打印");
    print_another();
}

fn print_another(){
    println!("另一个函数中打印");
}
```

上述代码正常执行，不需要将后一个函数放到前面。

## 参数

&emsp;参数变量/形参（parameter），实参（argument）。必须在定义函数的时候，显式声明每个参数类型。

## 表达式与语句

&emsp;rust中分为表达式（expression）与语句（statement），表达式可以赋值给一个变量，语句则不行，这与C++里有所不同。

```rust
let y = 6;
let x = (let y = 6); // 报错
let z = y = 6; // 报错
let h = {  // h = 9
    let m = 3;
    m*3 // 注意没有分号
};  // 这里有分号

```

## 函数返回值

&emsp;必须显式声明返回类型，使用箭头。

```rust
fn test() -> i32 {
    let m = 10;
    m + 100 // 注意没有分号，该函数返回110
}
```

如果没有无分号语句，函数会默认返回一个空元组，则与显式声明的类型不同会报错。

# 第四章 流程控制

## 分支

### if表达式

&emsp;`if`的条件部分必须明确为布尔类型的。

```rust
let num = 3;
if num > 0 {
    println!("num大于0");
} else if num == 0 {
    println!("num等于0");
} else {
    println!("num小于0");
}

// 下面这种写法会报错
if num  {
    println!("num大于0");
} else if num == 0 {
    println!("num等于0");
} else {
    println!("num小于0");
}
```

除此之外，上述[函数体与代码块](#函数体与代码块)中我们可以用表达式赋值给变量，if也是表达式，所以可以用if来赋值，值得注意的是，多个分支返回的类型必须一致。

```rust
let n = -32;
let m = if n < 0 {  
    -n  // 注意没分号
} else {
    n
};  // 这里有分号
```



### match表达式

&emsp;match表达式同样可以用来赋值，并且，阅读此小节，需要先阅读[枚举](#枚举)小节先。match表达式同样只会执行第一个匹配的。

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
	// 后面为美国所有的州
}
enum Coin{
    Penny,
    Nickel,
    Dime,
    Quarter(UsState), // 这个枚举值是一个UsState类型的
}

fn value_in_cents(coin: Coin) -> u32{
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("是{:?}州的币", state);
            25
        },
    }
}

```

match必须穷尽所有取值，因此可以使用通配符来穷尽其他取值

```rust
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    _ => (),
}
```

### if let分支

if let相当于match的语法糖，因为match需要穷尽分支

```rust
match coin{
    Coin::Quarter(state) => println!("State = {:?}", state),
    _ => ()
}

// 上述可以写成
if let Coin::Quarter(state) = coin {
    println!("State = {:?}", state);
}
```



## 循环

### loop表达式

&emsp;loop作为一个表达式，同样可以作为返回值。

```rust
let mut i = 0;
let m = loop{
    i += 1;
    
    if i == 10 {
        // 有分号，使用break终止运行，返回值放到break后面
        break i * 2; 
    }
};
```



### while语句

```rust
while true {
    // 为true就一直执行死循环
    // 同样可以用break跳出循环
    break;
}
```



### for语句

```rust
let a = [1, 2, 3, 4, 5];
// 打印1 2 3 4 5
for e in a.iter() {
    println!("the value is {}", e);
}

// 打印4 3 2 1，rev是反的意思，(1..5)生成1 2 3 4
for num in (1..5).rev(){
    println!("value = {}", num);
}
```



# 第五章 内存空间

&emsp;在string中，与其他语言类似，默认是浅拷贝。如果要深拷贝，则需要调用`clone()`方法。rust默认带了GC（自动回收）机制。

# 第六章 结构体、枚举

## 结构体

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

let user1 = User{
    email: String::from("someone@example.com"),
    username: String::from("雨探青鸟"),
    active: true,
    sign_in_count: 1,
};

let user2 = User{ // 除了email和username之外都与user1相同
	email: String::from("another@example.com"),
    username: String::from("yoyocat"),
    ..user1
};
user1.username; // "雨探青鸟"
```

除此之外，还可以有如下匿名写法

```rust
struct Color(u8, u8, u8);
struct Point(i64, i64);
let red = Color(255, 0, 0);
let o = Point(0, 0);
```

打印结构体

```rust
#[derive(Debug)]  // 增加注解
struct Rectangle {
    width: u32,
    height: u32,
}

let rect1 = Rectangle {width: 30, height: 50};
println!("rect1 = {:?}", rect1); // 正确，一行打印
println!("rect1 = {:#?}", rect1); // 正确，多行打印
println!("rect1 = {}", rect1); // 错误
```

定义方法

```rust
struct Rectangle{
    width: u32,
    height: u32,
}
impl Rectangle{
    fn area(&self) -> u32{
        self.width * self.height
    }
    
    // 下面是关联函数，不传入&self的
    fn square(size: u32) -> Rectangle{
        Rectangle{width: size, height: size}
    }
}


// 可以写多个impl块
impl Rectangle{ 
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

## 枚举

```rust
enum IpAddrKind {
    V4, V6,
}

let four = IpAddrKind::V4;
let six = IpAddrKind::V6;


// 还可以这么定义
enum IpAddr{
    V4(u8, u8, u8, u8),
    V6(String),
}
let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));

// 附带一句，标准库有两个结构体 Ipv4Addr 与 Ipv6Addr


// 下面还可以更复杂定义枚举
enum Message {
    Quit, 
    Move { x:i32, y:i32 },  // 包含一个匿名结构体
    Write(String), // 包含一个String
    ChangeColor(u8, u8, u8), // 包含三个u8
}

// 同样的，可以用impl来定义方法
impl Message {
    fn call(&self){
        
    }
}
```

&emsp;下面说一下空值，rust库中使用枚举来实现控制，而不像Python一样直接为空。

```rust
// 下面是官方库的内容
enum Option<T>{
    Some(T),
    None,
}

// 因为Option是默认导入的，所以不需要Option::来调用
let some_num = Some(5);
let some_string = Some("a string");
let absent_num: Option<i32> = None; // 即使是None，也需要指定类型

// 如何使用上述值呢？请看match表达式
```

# 第七章 常用类型

## vector

```rust
let v: Vec<i32> = Vec::new();

let mut v = vec![1, 2, 3]; // vec!是一个宏

v.push(4); // 在后面添加4，当然v必须是mut的
// 离开作用域v会自动销毁

// 索引方式有 &[] 和 get()
&v[2]; // 返回3的引用，越界会panic
v.get(2); // 返回一个Option<&T>，并且可以返回None而不至于程序panic

// 一个小细节
let first = &v[0];
v.push(6); // 会报错，因为上一行的代码有对v的元素的引用，所以不能更改v

for i in &v {
    // 遍历v
}
for ii in &mut v{
    *ii += 50; // 可以更改v的值，但是需要使用 *ii
}


// vec同样可以和枚举搭配使用，来存储不同的值
enum SpreadsheetCell{
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec! [
	SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```



## String

```rust
let mut s = String::new();

let s1 = "initial contents".to_string();

let s2 = String::from("initial contents");

s.push('l');
s.push_str("hello");
s[0]; // 'l'
```



## hash map

```rust
use std::collections::HashMap;
let mut scores = HashMap::new();
scores.insert(String::from("blue"), 10);
scores.insert(String::from("yellow"), 50);

let teams = vec![String::from("blue"), String::from("yellow")];
let initial_scores = vec![10, 50];
let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();
let score = scores.get(&String::from("blue"));

for(key, value) in &scores {
    println!("{}: {}", key, value);
}
```

# 第八章 错误与异常

&emsp;rust程序有一个不可恢复的错误`panic`，同时，rust还提供一个宏`panic!()`来打印一段错误提示信息。发生panic后，程序会沿着调用栈退出。除此之外，还可以通过更改toml文件来使得程序由沿着调用栈退出变为直接终止，以达到缩小二进制包的大小的目的。

```toml
[profile.release]
panic = 'abort'
```

下面演示一段`panic!`宏来处理打开文件的操作。

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main(){
    let f = File::open("hello.txt");
    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create ("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("由于“{:?}”创建文件失败", e),
            },
            other_error => panic!("发生未知错误“{:?}”", other_error),
        },
    }
}

```

&emsp;除此之外，还有`unwrap`和`expect`的方式。expect会将字符串作为错误输出，而unwrap只会默认输出错误信息。两者使用方法都是一样的。

```rust
let f1 = File::open("hello.txt").unwrap();
let f2 = File::open("hello.txt").expect("文件打开失败");
```

&emsp;如何将错误传递给调用者呢？有两种方式

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");
    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };
    let mut s = String::new();
    match f.read_to_string(&mut s){
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
```

第二种方式更加简单，使用?运算符。

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}

// 或者更简便
fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("hello.txt")?.read_to_string(&mut s)?;
    Ok(s)
}
```



# 第九章 泛型

## 泛型的几种应用场景

```rust
// 函数中
fn largest<T>(list: &[T]) -> T {
    
}

// 结构体中
struct Point<T>{
    x: T,
    y: T,
}
struct Point<T, U> {
    x: T,
    y: U,
}

// 枚举中
enum Option<T> {
    Some(T),
    None,
}
enum Result<T, E> {
    Ok(T),
    Err(E),
}

// 结构体方法中
struct Point(T){
    x: T,
    y: T,
}
impl<T> Point<T> {
    fn x(&self) -> &T{
        &self.x
    }
}
```

## trait

trait与其他语言中的接口类似。看下面例子，`NewsArticle`与`Tweet`实现`Summary`

```rust
pub trait Summary {
    fn sumarize(&self) -> String;
}

pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}
impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}
impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}

```

# 第十章 预设宏

- `asssert_eq!(a, b)`：判断a和b是否相等
- `panic!("")`：发生panic异常
- `assert!()`：测试
- `assert_eq!()`：判断等于
- `assert_ne!()`：判断不等于