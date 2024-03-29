---
title: 函数式编程——λ演算
date: 2023-07-31 21:40:33.598
updated: 2024-01-10 20:44:19.474
url: /archives/lambdacalculus
categories: 
- 编程语言
tags: 
- lambda
- 函数式编程
---

# 前言

&emsp;[邱奇-图灵论题](https://zh.wikipedia.org/zh-cn/%E9%82%B1%E5%A5%87-%E5%9B%BE%E7%81%B5%E8%AE%BA%E9%A2%98)是关于可计算性理论的假设，对可计算性公式化表示的尝试有：邱奇的λ演算，图灵的图灵机，函数递归求值。因此，λ演算与函数式编程是非常重要的，虽然其应用没有图灵机那么广泛。

&emsp;另外，本文讲解除了第一章之外，其他章节并没有顺序，而是随心所欲想到哪写到哪。本文的许多记法的写法不一定严谨，这是因为目前有很多中写法，而本文也只是采用作者较为习惯的写法而已。

- 一些学习网站：
    - [goodmath博客](http://goodmath.blogspot.com/2006/05/my-favorite-calculus-lambda-part-1.html)
    - [goodmath博客翻译](https://cgnail.github.io/academic/lambda-1/)
    - [B站转载的视频](https://www.bilibili.com/video/BV1nZ4y1W7yX)
    - [YouTube视频合集](https://www.youtube.com/watch?v=v1IlyzxP6Sg&list=PLDAqk5znTEXeQwDstVk5uzsNmj3RTMlVg)，作者：RU Computer Science
    - [斯坦福哲学百科](https://plato.stanford.edu/entries/lambda-calculus)
    - [palmstrom](http://palmstroem.blogspot.com/2012/05/lambda-calculus-for-absolute-dummies.html)
    - [palmstrom翻译](https://zhuanlan.zhihu.com/p/30510749)

# 第一章 从函数开始

## 从相同点出发

&emsp;在高中的时候就学习过函数，如 $f(x) = x^2+2x+m$，这表示一个函数，自变量为`x`。如果我们要求`x=2`时，函数值是多少，$f(2)=2^2+2\times2+m$，很容易就能求出来，然后进一步简化得到$f(2)=8+m$。因为我们知道，只需要把所有的`x`替换成`2`就行。那么，求`f(a)`是不是把`x`替换成`a`即可。这一个计算过程，可以看作是函数`f(x)`作用在量`a`上。

&emsp;我们如何用λ演算来表示上述函数呢？我们可以写作$\lambda x . x^2+2x+m$，这种写法也许并不严谨，这一整个式子可以看作一个函数，如何将这个函数作用到其他量上呢？我们可以写作$\lambda x.x^2+2x+m\quad a$，这种写法也许也不严谨，但是我们可以如此表示将函数作用于量上。所以，接下来的本文都采用λ的形式，而不再采用高中的数学形式。

&emsp;值得注意的是，在高中函数中，我们把`f(x)`看作函数，`f(2)`看作是函数值。但是在λ演算，$\lambda x.e$看作是λ表达式或者函数，作用于`a`上，$\lambda x.e\quad a$也看作是λ表达式或者函数，他们是一种概念。也就是说，只有函数（或者说表达式），万事万物都是函数，包括自然数`0,1,2...`，逻辑`true or false`等等。（其中`e`表示一个表达式）

## 到不同点结束

&emsp;在此之前，我想先谈论两个概念，functions-as-sets（函数即集合）和functions-as-rules（函数即规则）。前者是说，如果两个函数，能够将相同的参数映射到相同的值上，那么这两个函数是相等的，其是extensional concept（扩展性的概念）。后者是说，如果两个函数能够将相同的参数映射到相同的值上，并且其映射规则也是相等的，那么这两个函数是相等的，其是non-extensional concept（非扩展性的概念）。

&emsp;在高中，我们将一个函数看作三部分组成，即定义域、对应规则和值域。当三部分都相等时，我们视两个函数相等。在λ演算中，对于两个函数相等的概念定义也是一样的。但是，既然写在这一小节，终究是有区别的。

&emsp;考虑$f(x)=x+1$与$g(x)=x+2-1$，这两个函数，我们通常会认为其等价。考虑$h(x)=(\sqrt{x})^2$与$e(x)=x$，这两个函数我们通常认为其不等价。而在λ演算中，这两个函数均不是等价的，因为我们无法在λ演算中认为2-1=1这种概念，或者说，λ演算并没有数字的概念，只有逻辑的概念。或者说，在我们已有认知的数学中，自然而然会把2-1自动换算成1，其实际上前者是加上1，后者是加上2再减去1，其作为规则的概念是不一样的，前者只有一次操作，后者有两次。

&emsp;对于上述$f(x)$与$g(x)$，我们认为其在外延(extensionally equivalent)上是等价的，在内涵上(intensionally equivalent)等价。此外，还有`hyperintensional`的概念，尚未搞清楚。

# 第二章 演算

## α-conversion

&emsp;什么是α-conversion呢？在上述例子中，我们定义$f(x) = x^2+2x+m$。那我们同样可以把这个函数写作$f(y) = y^2+2y+m$，在λ表达式中同样可以这样。如$\lambda x.x^2+2x+m$也可以写作$\lambda y.y^2+2y+m$，这个特性成为α-conversion。

## β-reduction

&emsp;什么是β-reduction呢，或者叫做β-conversion。在第一章函数中，我们将`a`替换掉`x`的过程（$\lambda x.x^2+2x+m\quad a \rightarrow a^2+2a+m$）称为β-reduction，它也是λ演算的核心。关于β-reduction，还可以记作$[f/x]e$，也就是在使用`f`替换表达式`e`中的所有出现的[自由变量](#第三章 量)`x`，当然，这种写法不一定严谨。比如，$[a/x]x^2+2x+m \rightarrow a^2+2a+m$。

&emsp;在本节中，我们使用了右箭头，使用右箭头来表示演算，而非等号。

## Church-Rosser Theorems

&emsp;如果$e\rightarrow f$且$e\rightarrow h$，那么必定存在一个最简形式g使得$f\rightarrow g$和$h\rightarrow g$，最简形式就是指不能继续演算，或者说继续演算仍然是其本身（或者周期性，周期性自己加的，存疑）的变成其本身，这种形式成为λ表达式的最简形式。上述说明，一个λ表达式的最简形式是唯一的。

# 第三章 量

&emsp;在这一章，我们将辨析一下自由变量(free variables, FV)与绑定变量(bound variables, BV)。$\lambda x.xy$中，绑定变量是x，y是自由变量。一个完整λ表达式，其所有变量都应该是绑定的，但是对于某些子表达式，我们探讨的时候可以有自由变量。前面所说的α-conversion，只能重命名绑定变量，自由变量是不可以随意更改的，因为自由变量会是其他λ的绑定变量。

# 第四章 柯里化

&emsp;在大学的高等数学课程中，我们会学到多元函数，如$f(x,y)=\sqrt{x^2+y^2}$，这是一个计算三角形斜边的函数。它有两个输入，一个是直角边x，一个是直角边y，最终会得到一个输出$f(x,y)$。假如，$x=3,y=4$，那么我们可以计算得到斜边长度是5。

&emsp;λ演算中，并没有多参数形式，λ演算只能由一个参数。那么，对于上述情况，应该如何应对呢？柯里化`Curring`。Haskell Currying是一名数学家和逻辑学家，提出了一种接收多个参数的函数转换成一系列接收单个参数的函数链。下面，将给出JavaScript的代码演示。

```javascript
// 下面是使用一般方式计算三角形斜边长度
function HypotenuseLength(x, y){
     return Math.sqrt(x*x + y*y);
}

// 下面是使用柯里化计算三角形斜边长度
function CurringHypotenuseLength(x){
    return function (y){
        return Math.sqrt(x*x + y*y);
    }
}

// 接下来，我们将会调用一下两个函数
console.log(HypotenuseLength(3, 4)); // 5
console.log(CurringHypotenuseLength(3)(4)); // 5
```

&emsp;如何在λ演算中表示呢？$\lambda x.\lambda y.\sqrt{x^2+y^2}$，让我们为其加上括号使得演算顺序更加清晰$(\lambda x.(\lambda y.\sqrt{x^2+y^2}))$。在λ演算中，单个字母表示一个变量，这不同于编程中，现在讨论的是数学的抽象问题，请不要把这种变量与编程中变量的命名混为一谈。上述式子可以简写$\lambda xy.\sqrt{x^2+y^2}$，这些写法只是习惯不同，不影响λ演算。接下来对上述例子进行演算。
$$
(\lambda x.(\lambda y.\sqrt{x^2+y^2})\quad3)\quad4 \quad或者\quad \lambda xy.\sqrt{x^2+y^2}\quad3\quad4 \rightarrow \\
\lambda y.\sqrt{3^2+y^2}\quad4 \rightarrow \\
\sqrt{3^2+4^2} = 5
$$
等号是已有数学体系的运算，箭头才表示λ演算过程，或者说β-reduction。由上述过程可以看出，λ演算是从左到右的，也就是，先用3替换x，然后用4替换y。

&emsp;这样，便是Curring在函数式编程的应用了。对于多参数，则同理可得。值得注意的是，我们常常强调，万事皆为函数才可以有Curring的过程。注意，在后续章节的函数中，我仍然以多参数计算，其中自动进行柯里化了，则不再强调柯里化了，只要知道其中有个柯里化的过程即可。

# 第五章 逻辑布尔

## 定义true和false

&emsp;这不像自然数，使用1来表示一个，成为大家公认的看法。我们在布尔中，使用T表示true，使用F表示false。
$$
T:\quad \lambda ab.a \\
F:\quad \lambda ab.b
$$
第一行，选取第一个参数的表示真，也就是T；第二行，选取第二个参数表示假，也就是F。当然，可以互换的，但是后面也得还。读者可以尝试第一个参数表示假，第二个参数表示真，然后再自己重复下面几节的工作。

## 定义与或非

&emsp;与或非分别用 `& | !`来表示吧！当然，毫无疑问的是，其中最简单的应该是非运算，毕竟只接收一个参数。

**提示：**读者可以先自行尝试定义非，给一句提示，在上面我们定义T与F是。定义T是：T后面的两个参数选第一个；定义F是，F后面的两个参数选第二个。现在，考虑`T F T`与`F F T`会演算成什么呢？如果读者还未能定义出来，那么请看作者给出的定义。

由上述提示，我们可以看到，$T\ \ F\ \ T\ \  \rightarrow F$与$F\ \ F\ \ T\ \ \rightarrow T$，这样子，我们是不是得到了T变成F与F变成T了。所以，定义非应该是下面这样，并给出了`!T`的演算过程。
$$
!:\quad \lambda a.a(F)(T) = \lambda a.a(\lambda ab.b)(\lambda ab.a) \\
!T:\quad (\lambda a.a(\lambda ab.b)(\lambda ab.a))\ \ (\lambda ab.a) \\
\rightarrow (\lambda ab.a)\  (\lambda ab.b)\ (\lambda ab.a) \\
\rightarrow \lambda ab.b = F
$$
&emsp;根据上面的提示与定义，读者尝试自行定义与和或出来。当然，无论是与还是或，都应该是前缀表达式的写法。下面我会给出我的提示与定义，可酌情阅读。

**提示**：请看下表，下标是八个λ演算过程。

|  T   |  T   |  T   |  →   |  T   |
| :--: | :--: | :--: | :--: | :--: |
|  T   |  T   |  F   |  →   |  T   |
|  T   |  F   |  T   |  →   |  F   |
|  T   |  F   |  F   |  →   |  F   |
|  F   |  T   |  T   |  →   |  T   |
|  F   |  T   |  F   |  →   |  F   |
|  F   |  F   |  T   |  →   |  T   |
|  F   |  F   |  F   |  →   |  F   |

根据上表，我们需要选取3个F和1个T作为与的结果，并且，这四行需要有一个位置的值是一样的，所以，我们看第2、4、6、8行。
$$
\&:\quad \lambda ab.abF \\
\&\ \ T \ \ T:\quad(\lambda ab.abF)\ \ T\ \ T\rightarrow T\ T\ F\rightarrow T\\
\&\ \ T \ \ F:\quad(\lambda ab.abF)\ \ T\ \ F\rightarrow T\ F\ F\rightarrow F\\
\&\ \ F \ \ T:\quad(\lambda ab.abF)\ \ F\ \ T\rightarrow F\ T\ F\rightarrow F\\
\&\ \ F \ \ F:\quad(\lambda ab.abF)\ \ F\ \ F\rightarrow F\ F\ F\rightarrow F
$$


同理，我们看第1、2、4、5行，就可看作是或的定义了。
$$
|:\quad \lambda ab.aTb\\
|:\quad (\lambda ab.aTb)\ \ T\ \ T\rightarrow T\ \ T\ \ T\rightarrow T\\
|:\quad (\lambda ab.aTb)\ \ T\ \ F\rightarrow T\ \ T\ \ F\rightarrow T\\
|:\quad (\lambda ab.aTb)\ \ F\ \ T\rightarrow F\ \ T\ \ T\rightarrow T\\
|:\quad (\lambda ab.aTb)\ \ F\ \ F\rightarrow F\ \ T\ \ F\rightarrow F
$$

## 定义条件判断

&emsp;通过上述三节定义，我们应该能够很快定义条件判断了，当然，应该明确if是有三个输入。
$$
if:\quad \lambda abc.abc\\
if\ \ T\ \ e_1\ \ e_2:\quad \lambda abc.abc\ \ T\ \ e_1\ \ e_2\rightarrow e_1\\
if\ \ F\ \ e_1\ \ e_2:\quad \lambda abc.abc\ \ F\ \ e_1\ \ e_2\rightarrow e_2
$$
上述说明，如果为真，执行函数$e_1$，如果为假，执行函数$e_2$。

## 代码

接下来，我将使用JavaScript写出本章节的代码定义。

```javascript
var T = (a, b) => a;
var F = (a, b) => b;
var NOT = (a) => a(F, T);
var AND = (a, b) => a(b, F);
var OR = (a, b) => a(T, b);
var IF = (a, b, c) => a(b, c);
function show(str, a){
	if(a(true, false)){
		console.log(str + " = T");
	}else{
		console.log(str + " = F");
	}
}
function e1(){
	return "e1函数";
}

function e2(){
	return "e2函数";
}

show("T", T);
show("F", F);
show("NOT T", NOT(T));
show("NOT F", NOT(F));
show("AND T T", AND(T, T));
show("AND T F", AND(T, F));
show("AND F T", AND(F, T));
show("AND F F", AND(F, F));
show("OR T T", OR(T, T));
show("OR T F", OR(T, F));
show("OR F T", OR(F, T));
show("OR F F", OR(F, F));
show("IF T e1 e2", IF(T, e1, e2));
show("IF F e1 e2", IF(F, e1, e2));
```

#

# 第六章 自然数

## 定义自然数

&emsp;我们前面有说，所有的东西都应该被定义，不能使用已有的数学体系。那么，在本章中，将来定义一下自然数。
$$
0:\quad \lambda sz.z \\
1:\quad \lambda sz.s(z)\\
2:\quad \lambda sz.s(s(z))\\
3:\quad \lambda sz.s(s(s(z)))\\
...
$$

## 定义加1

看样子，只是里面的s有几个就表示几而已，首先它还是比较符合直观的。但是它是否严谨呢？也就是，当4、5…是否可以用同样的定义来得出呢？那么我们先定义一个函数，其作用是求解后一个数。
$$
S:\quad \lambda abc.(b(abc))
$$
注意，这里我特地写作`(b(abc))`，只是为了说明，后面一个整体是前面函数的表达式(expression)部分。我们接下来演算一下S0与S1。
$$
S0:\quad (\lambda abc.(b(abc)))\ \ \ (\lambda sz.z)  \\ 
\rightarrow \lambda bc.b((\lambda sz.z)bc) \quad（用\lambda sz.z将a替换） \\
\rightarrow \lambda bc.b((\lambda z.z)c) \quad（用b将\lambda sz.z中的s替换） \\
\rightarrow \lambda bc.b(c) \quad（用c将\lambda z.z中的z替换）\\
\rightarrow \lambda sz.s(z) \quad（字母只是符号，这里就是1的定义）
$$
下面，再演算一下S1，之后就可以自己尝试推理一下S3等等了。
$$
S1:\quad (\lambda abc.(b(abc)))\ \ \ (\lambda sz.s(z)) \\
\rightarrow \lambda bc.b((\lambda sz.s(z))bc) \quad（用\lambda sz.s(z)替换a）\\
\rightarrow \lambda bc.b((\lambda z.b(z))c) \quad（用b替换\lambda sz.s(z)的s）\\
\rightarrow \lambda bc.b(b(c)) \quad （用c替换\lambda z.b(z)中的z）\\
\rightarrow \lambda sz.s(s(z))\quad（字母同样只是符号，这里就是2的定义）
$$

## 定义加法

&emsp;然后，加法的定义其实也是S，我们演算一遍3+2=5，也就是$3S2\rightarrow 5$，并且，这里在中间过程我会使用3、2作为符号来替换一长串。
$$
3S2: \quad (\lambda sz.s(s(s(z)))) \quad(\lambda abc.b(abc))\quad (\lambda sz.s(s(z))) \\
\rightarrow S(S(S(2))) \\
\rightarrow S(S(3)) \\
\rightarrow 5
$$
先看第二行怎么来的，第一行其实是：$(\lambda sz.s(s(s(z))))\quad S \quad 2$，然后$S \quad 2$作为前面的两个参数，直接先替换，即S替换s，2替换z。得到第二行。然后我们又知道S(2)可以演算成3的，所以得到第四行，同理得到第五行。

&emsp;事实上，上面的`3+2:3S2`这种写法，并不符合λ表达式的写法，λ表达式是采用[波兰表达式](https://zh.wikipedia.org/zh-hans/%E6%B3%A2%E5%85%B0%E8%A1%A8%E7%A4%BA%E6%B3%95)（或者说前缀表达式）的写法，应该写成`+ 3 2：S 3 2`的形式。因此，重新定义加法
$$
+:\quad \lambda dabc.db(abc)\\
+\ \ 3\ \ 2:\quad \lambda dabc.db(abc)\ \ (\lambda sz.s(s(s(z))))\ \ (\lambda sz.s(s(z)))\\
\rightarrow \lambda bc.(\lambda sz.s(s(s(z))))b((\lambda sz.s(s(z)))bc)\\
\rightarrow \lambda bc.(\lambda z.b(b(b(z))))(\lambda z.b(b(z))c)\\
\rightarrow \lambda bc.(\lambda z.b(b(b(z))))(b(b(c)))\\
\rightarrow \lambda bc.(b(b(b(b(b(c)))))) = 5
$$


## 定义乘法

&emsp;直接给出乘法的定义：注意这里我没有把`a(bc)`括起来，希望读者也能明白，`a(bc)`表示表达式，而不是bc表示参数（或者说将a作用于bc，因为a还是符号，不是函数，如果使用函数替换a，那么才会进行演算）。
$$
M:\quad \lambda abc.a(bc)
$$
演算一下M32：注意，这里跟一般写法不一样，不是写成3×2，而是写成× 3 2，这个是[波兰表达式](https://zh.wikipedia.org/zh-hans/%E6%B3%A2%E5%85%B0%E8%A1%A8%E7%A4%BA%E6%B3%95)（或者说前缀表达式）。
$$
M\ 3\ 2:\quad (\lambda abc.a(bc)) \quad (\lambda sz.s(s(s(z)))) \quad (\lambda sz.s(s(z))) \\
\rightarrow \lambda c.(\lambda sz.s(s(s(z))))(\lambda sz.s(s(z))c)\quad （使用3、2替换M中的a、b）\\
\rightarrow \lambda c.(\lambda sz.s(s(s(z))))(\lambda z.c(c(z))) \quad （bc是括号括起来，所以先计算，c替换2中的s）\\
\rightarrow \lambda c.(\lambda z.H(H(H(z)))) \quad（简写\lambda z.c(c(z))为H）\\
\rightarrow \lambda c.(\lambda z.c(c(c(c(c(c(z))))))) \quad （看下面，这里6个c）\\
\rightarrow \lambda cz.c(c(c(c(c(c(z)))))) \quad（这个就是6的定义） \\
$$
其中，单独演算一下`H(H(H(z)))`
$$
H(H(H(z)))\rightarrow H(H(\lambda z.c(c(z))) \ \ z)\rightarrow H(H(c(c(z)))) \\
\rightarrow H((\lambda z.c(c(z))) \ \  (c(c(z)))) \rightarrow H(c(c(c(c(z)))))\\
\rightarrow c(c(c(c(c(c(z)))))) \quad（6个c）
$$


可以看出，M的定义是符合乘法的。

## 结论

&emsp;看了前面的三个小节，我们看得出来，只要定义了自然数，定义好乘法，数学有种天然的逻辑关系。图灵机是基于状态的，而λ演算是基于运算的。在图灵机内，使用已有的数学体系定义好的自然数，自然而然简单很多。但是在λ演算中，并没有使用已有的数学体系。上述也只是一种定义方法，其实还是可以有其他定义法的。因为λ演算是不保存状态的，所以使用λ演算得出的函数式编程，如果用成λ演算，而不预先定义好数字与运算符等数学符号，那么性能是有所欠缺的。所以，现在的许多的函数式编程语言，其实是定义好了一些预设的数学符号的。比如加法，减法等。程序员只需要考虑符合自己项目工作的函数，又由于函数式编程只要定义正确，后续的演算均是逻辑自洽的，所以bug是比较少的，不会像其他语言一样，容易写错一个数字字母之类的导致整个函数有小bug，查不出来，函数式编程语言要么是大的逻辑bug，要么没有bug。

# 第七章 递归

## Y Combinator

&emsp;Y Combinator，又称为paradoxical combinator（注：作者没有很好的翻译，不知如何区翻译）。一个函数以函数G作为参数，但是演算后又返回G(YG)，继续演算，又返回G(G(YG))，一直演算下去就变成G(G(G(…G(YG))))。假如Y定义成$(\lambda fx.f(xx))\ \ (\lambda x.f(xx))$，对于任意表达式G。
$$
YG:\quad ((\lambda fx.f(xx))\ \ (\lambda x.f(xx)))\ \ G\\
\rightarrow (\lambda x.G(xx)) \ \ (\lambda x.G(xx)) \quad （将G替代f） \\
\rightarrow G((\lambda x.G(xx))\ \ (\lambda x.G(xx)))\quad （将后面的\lambda x.G(xx)替换前面的x） \\
G(YG)（这是因为(\lambda x.G(xx))(\lambda x.G(xx))就是由YG演算而来的）
$$
上述的Y是一个[不动点组合子](https://zh.wikipedia.org/wiki/%E4%B8%8D%E5%8A%A8%E7%82%B9%E7%BB%84%E5%90%88%E5%AD%90)（Fixed-point combinator）的例子，由于λ表达式的函数是匿名的，所以没法像编程语言那样去调用如下面求阶乘的方法一样。

```javascript
// 函数名本身就是一个语法糖了
var F = n => n == 0 ? 1 : n*F(n-1);
F(5); // 返回 120，计算了5!
```

使用不动点组合子可以使得匿名函数也能进行递归了，可以参考这篇文章[重新发明 Y 组合子 JavaScript(ES6) 版](http://picasso250.github.io/2015/03/31/reinvent-y.html)。下面来演示一下如何写

```javascript
// 既然函数体内F(n-1)不能用F，那么我们可以把F作为参数传进来
var F = (f, n) => n==0 ? 1 : n*f(f, n-1); 
F(F, 5); // 返回120，计算了5!

// 完全避免一下语法糖，也就是把上面扩展写
// 我们不用F，直接写两遍F等号右边，注意括号的写法，要满足JavaScript的语法
((f, n) => n==0 ? 1 : n*f(f, n-1))((f, n) => n==0 ? 1 : n*f(f, n-1), 5);  // 同样返回120
(f => n => n == 1 ? 1 : n * f(f)(n-1)) (f => n => n == 1 ? 1 : n * f(f)(n-1)) (5); // 柯里化一下，改成单参数
```

&emsp;接下来，我们使用λ表达式来描述一下整个过程，在此之前，我们假设我们定义好了$IF(==\ n\ 0)\ \ 1\ \ (*\ \ n\ \ (f\ \ (-\ \ n\ \ 1)))$，这个是阶乘最核心的部分，其中最重要的是f，我们会简写成$BfC$，不用管B、C分别代表什么，只要记得f是有些变化的就行。
$$
F = \lambda n.BFC = (\lambda n.IF(==\ n\ 0)\ \ 1\ \ (*\ \ n\ \ (F\ \ (-\ \ n\ \ 1))))
$$
但是由于λ表达式应该是匿名的，不应该由名字，函数体内的F则应该去除掉，我们就可以在外层套一层参数。
$$
\lambda f.\lambda n.BfC = \lambda f.\lambda n.IF(==\ n\ 0)\ \ 1\ \ (*\ \ n\ \ (f\ \ (-\ \ n\ \ 1)))
$$
上述由于整个λ表达式是合法的，所以我缩写成$G=\lambda f.\lambda n.IF(==\ n\ 0)\ \ 1\ \ (*\ \ n\ \ (f\ \ (-\ \ n\ \ 1)))$，演算一下5!。
$$
Y \ \ G\ \ 5\rightarrow G\ \ (YG)\ \ 5\\
\rightarrow (\lambda f.\lambda n.IF(==\ n\ 0)\ \ 1\ \ (*\ \ n\ \ (f\ \ (-\ \ n\ \ 1))))\ \ (YG) \ \ 5\\
\rightarrow IF(==\ 5\ 0)\ \ 1\ \ (*\ \ 5\ \ ((YG)\ \ (-\ \ 5\ \ 1)))（分别把YG和5带入f和n中）\\
\rightarrow *\ \ 5\ \ ((YG)\ \ 4)（这里降到了YG4）\\
\rightarrow *\ \ 5\ \ (*\ \ 4\ \ ((YG)\ \ 3))\\
\rightarrow 120
$$
单独演算一下YG0
$$
Y \ \ G\ \ 0\rightarrow G\ \ (YG)\ \ 0\\
\rightarrow (\lambda f.\lambda n.IF(==\ n\ 0)\ \ 1\ \ (*\ \ n\ \ (f\ \ (-\ \ n\ \ 1))))\ \ (YG) \ \ 0\\
\rightarrow IF(==\ 0\ 0)\ \ 1\ \ (*\ \ 0\ \ ((YG)\ \ (-\ \ 0\ \ 1)))（分别把YG和1带入f和n中）\\
\rightarrow 1\\
$$
所以，计算到YG0就变成1，最终式子变为
$$
*\ \ 5\ \ (*\ \ 4\ \ (*\ \ 3\ \ (*\ \ 2\ \ (*\ \ 1\ \ (1)))))=120
$$


## Y怎么来的

&emsp;回到上面的JavaScript代码中，上面是具体业务，也就是需要抽象出来一个Y。

```javascript
(f => n => n == 1 ? 1 : n * f(f)(n-1)) (f => n => n == 1 ? 1 : n * f(f)(n-1)) (5); 
// 毫无疑问，我们记F为f => n => n == 1 ? 1 : n * f(f)(n-1)，那么上面是不是就是
F(F)(5);
```

开始抽象。首先，先抽象掉F(F)。

```javascript
(g=>g(g))(F);// 函数 v=>v(v)作用与F，那么是不是抽象出来了

// 扩展写法，不用记作F
(g=>g(g))(
    f => n => n == 1 ? 1 : n * f(f)(n-1)
)(5);
```

业务还很耦合，所以单独看业务这一段

```javascript
f => n => n==1?1:n*f(f)(n-1); // 这是我们的业务，需要对f(f)抽象一下

f => 
	( d => n => n==1?1:n*d(n-1) )  (f(f)); // f(f)作为参数d传进去

f =>
	( d => n => n==1?1:n*d(n-1) )  (v=>f(f)(v)) 
// 这是js中f(f)会溢出，所以写成这样 (v=>f(f))(v)

// 中间这里就是阶乘定义了，并且不含d、n以外的变量，抽象出来
{ d => n => n==1?1:n*d(n-1) } 

(fact0 => f =>
	fact0(v=>f(f)(v)))   ( d => n => n==1?1:n*d(n-1) ); // 将阶乘的定义以参数传入

// 计算5的阶乘，这是阶乘调用
(g=>g(g))(  // 外层
    (fact0 => f =>
		fact0(v=>f(f)(v)))   ( d => n => n==1?1:n*d(n-1) )  // 内层
)(5);

// 去掉(5)，抽象成阶乘函数
(g=>g(g))
( 
    (fact0 => f =>
		fact0(v=>f(f)(v)))   ( d => n => n==1?1:n*d(n-1) )
)


// 脱离业务，也就是不写业务，业务作为参数传入
e=>(g=>g(g))
(  
    (fact0 => f =>fact0(v=>f(f)(v)))(e)
)


// (v=>v(v)) ()，也即是将括号里面的带入v
e=>
	((fact0 => f =>fact0(v=>f(f)(v)))(e))
	((fact0 => f =>fact0(v=>f(f)(v)))(e))

// 把e应用到fact0一下
e=>
	(f=>e(v=>f(f)(v)))
	(f=>e(v=>f(f)(v)))

// 后面两个是一样的，并且v=>f(f)(v)写回f(f)
e=> 	(u=> u(u))
		(f=>e(f(f)))
```

写成λ表达式
$$
\lambda e.(\lambda u.u u)\lambda x.e(x x)\\
\rightarrow \lambda e.(\lambda x.e(xx))(\lambda x.e(xx))\\
\rightarrow (\lambda fx.f(xx))\ \ (\lambda x.f(xx)) = Y
$$


&emsp;**作者评语**：写其他地方的时候，我尚未感觉到难度很大。写完这一小节，我觉得我只能说刚触摸到λ演算领域的大门而已。也不得不说，如果真的使用λ演算作为的计算机，那么它的入门门槛可能真的很高。不得不说，图灵机设计的真的是简单高效啊，保存了状态（状态可以看作一般的定义，如自然数啊、类结构体等啊）。

