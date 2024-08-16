---
title: 工程矩阵理论第一章
date: 2024-06-04 15:15:08.0
updated: 2024-06-09 20:37:55.131
url: /archives/gong-cheng-ju-zhen-li-lun-di-yi-zhang
categories: 
- 数学
tags: 
- 矩阵
---



# 前言

本文仅仅作为考试复习用，涉及知识较为浅显，若有不懂的符号，请看[符号记法](#符号记法)



# 线代基础

-   矩阵的秩：记为$r(A)$

    -   $AB=0\rightarrow r(A)+r(B)\leq n$​
    -   $r(A+B)\leq r(A)+r(B)$
    -   $r(AB)\leq r(A),r(B)$
    -   $A_{s\times n}B_{n\times t}  = 0\Rightarrow r(A)+r(B)\leq n$
    -   $r(A_{s\times n}B_{n\times t})\geq r(A)+r(B)-n$

-   矩阵的迹：记为$tr(A)$

    -   $tr(A)=\sum\lambda_i=\sum{a_{ii}}$

-   行列式：记为 $\det(A)$

    -   $\det(kA)=k^n\det(A)$
    -   $\det(AB)=\det(A)\det(B)$
    -   $\det(A^{-1})=\left(\det(A)\right)^{-1}$
    -   $\det(A+B)\neq\det(A)+\det(B)$ ：往往都是不等于，特殊情况才会等于。

-   齐次线性方程组：

    >   可以写成：$A_{s\times n}x=0$
    >
    >   求解方法，先把A初等行变换变成阶梯矩阵，可以得到第$r+1,r+2,\dots,n$列不存在非零首元，第$1,2,\dots,r$存在非零首元。
    >
    >   $r(A)=r$，基础解系为：$(x_{11},x_{12},\dots,x_{1r},1,0,\dots,0)^T,(x_{21},x_{22},\dots,x_{2r},0,1,\dots,0)^T,(x_{m1},x_{m2},\dots,x_{mr},0,0,\dots,1)^T$共$m=n-r$个基础解，就可以得到通解了。

# 线性空间

## 定义

设$V$是一个非空集合，$F$是一个数域，线性空间记为$V(F)$或者$V_F$。满足下面两条**线性运算**就是线性空间了：

***注：*** 在学习工程矩阵理论中，必须明确一下 **集合，数域，元素，向量** 这几个概念。请看[符号记法](#符号记法)

-   加法：加法指的是 **元素** 的加法，下面的加法`+`仅仅表示一种加法的笼统概念，并非特指已学习数学体系中的某个加法

    -   $\forall \alpha,\beta\in V$均有唯一$\gamma \in V$满足$\gamma=\alpha+\beta$：加法封闭性
    -   $A_1:\forall \alpha,\beta\in V, \alpha+\beta=\beta+\alpha$
    -   $A_2:\forall\alpha,\beta,\gamma\in V, (\alpha+\beta)+\gamma=\alpha+(\beta+\gamma)$
    -   $A_3:\exist \mathbf 0\in V,\forall\alpha\in V\rightarrow\alpha+\mathbf 0=\alpha$，其中零元素也有写作$\theta$
    -   $A_4:\forall\alpha\in V,\exist\beta\in V\rightarrow \alpha+\beta=\mathbf 0$

    >   $A_1,A_2$分别是交换律和结合律，$A_3,A_4$​分别是 **零元素** 和 **逆元素**

-   数乘：数乘指的是 **元素** 和 **数域** 的乘法，下面的加法`·`仅仅表示一种数乘的笼统概念，并非特指已学习数学体系中的某个数乘

    -   $\forall k\in F,\forall \alpha\in V$
    -   $M_1:\forall\alpha\in V,1\cdot\alpha=\alpha$
    -   $M_2:\forall k,l\in F,\forall\alpha\in V,k\cdot(l\cdot\alpha)=(k\cdot l)\cdot\alpha$
    -   $M_3:\forall k,l\in F,\forall\alpha\in V,(k+l)\cdot\alpha=k\cdot\alpha+l\cdot\alpha$
    -   $M_4:\forall k\in F,\forall\alpha,\beta\in V,k\cdot(\alpha+\beta)=k\cdot\alpha+k\cdot\beta$

## 子空间

若$V(F)$为线性空间，且$W\subset V$，且$V$上的线性运算在$W$上也满足，那就可以称为子空间了，记为$W\leq V$。
$$
W\leq V\Leftrightarrow \\ \forall \alpha,\beta\in W,\alpha+\beta\in W;\quad and\quad \forall \alpha\in W,\forall k\in F,k\alpha\in W\Leftrightarrow\\
\forall k,l\in F,\forall \alpha\beta\in W,k\alpha+l\beta\in W
$$
生成元与生成系：若 $W=\left\{\sum\limits_{i=1}^{k}x_i\alpha_i\Big|\forall x_i\in F\right\}$，称 $W$ 为 $\left\{\alpha_i\right\}_1^k$ 生成的的子空间，记为 $W=L\left[\alpha_1,\alpha_2,\dots,\alpha_k\right]=\mathrm{span}{\alpha_1,\alpha_2,\dots,\alpha_k}$，其中，$\left\{\alpha_i\right\}_1^k$ 称为 **生成系** ，$\alpha_i$​ 称为 **生成元**。

## 基、维数

线性空间 $V$ 由 $n$ 个线性无关的向量 $\alpha_1,\alpha_2,\dots,\alpha_n$ 生成。

1.   称 $\alpha_1,\alpha_2,\dots,\alpha_n$ 为 **基**；
2.   称 $n$ 为 **维数**，记为 $\dim V=n$​；
3.   特别的，零子空间的维数为0；

## 基扩充

若 $W\leq V,\dim V=n$， $\alpha_1,\alpha_2,\dots,\alpha_r$ 为 $W$ 的基，必可扩充 $V$ 的基为 $\alpha_1,\alpha_2,\dots,\alpha_r,\alpha_{r+1},\alpha_{r+2},\dots,\alpha_n$。

## 坐标向量

设 $\alpha_1,\alpha_2,\dots,\alpha_n$ 为 $V(\dim V=n)$ 的基，$\xi=\sum\limits_{i=1}^nx_i\alpha_i\in V$，称 $(x_1,x_2,\dots,x_i)$ 为 **坐标**，列向量 $(x_1,x_2,\dots,x_i)^T$ 为 **坐标向量**。

过渡矩阵：若 $(\beta_1,\beta_2,\dots,\beta_n)=(\alpha_1,\alpha_2,\dots,\alpha_n)P$ 称 $P$ 为基 $\left\{\alpha_i\right\}_1^n$ 到基 $\left\{\beta_i\right\}_1^n$​ 的过渡矩阵

# 交与和

## 定义

设 $V_1,V_2$ 是线性空间 $V$ 的两个子空间，称
$$
和：V_1 + V_2=\left\{ \alpha_1+\alpha_2\big| \forall\alpha_1\in V_1,\forall\alpha_2\in V_2 \right\}\\
交：V_1\cap V_2=\{\alpha\in V\big|\alpha\in V_1 且\alpha\in V_2\}
$$
 且和与交都是 $V$ 的子空间。

维数定理：$\dim V_1+\dim V_2=\dim(V_1+V_2)+\dim(V_1\cap V_2)$

## 直和定义

设$V_1,V_2\leq V\quad\forall \alpha\in V_1+V_2, \exist$ 惟一的 $\alpha_1\in V_1,\alpha_2\in V_2$ 使得 $\alpha=\alpha_1+\alpha_2$，则称 $V_1+V_2$ 是直和，记为 $V_1\oplus V_2$

以下命题等价：

1. $V_1+V_2$是直和
2. $\theta$的表示方式是惟一的
3. $V_1\cap V_2=\{\theta\}$
4. $\dim(V_1+V_2)=\dim V_1 + \dim V_2$
5. 将$V_1,V_2$的基合在一起就是$V_1+V_2$​​的基



# 线性映射

## 定义

$S\xrightarrow{f}T$， 称 $f$ 为线性映射，$s\in S,t=f(s)$ 中 $s$ 称为 **原象** ， $t$ 称为 **象** 。

满射：$f(S)=T$，就是 $T$ 中的全部元素都能找到一个原象。

单射：一个象只对应一个原象。

双射：满射加单射。

逆射：满足双摄才有逆射，相当于反函数。

-   线性映射：$V(F)\xrightarrow{f}U(F);\quad f(k\alpha+l\beta)=kf(\alpha)+lf(\beta),\quad(\forall \alpha,\beta\in V,\forall k,l\in F)$ ，称 $f$ 为 $V$ 到 $U$ 的线性映射，记为 $\mathrm{Hom}(V,U)$
-   线性变换：当上述 $U=V$ 时，记为 $\mathrm{Home}(V,V)$ 称为线性变换。
-   零映射：每个 $V$ 中的元素都被映射为 $\mathbf{0}_U$ 
-   恒等变换：映射到本身

## 线性映射的矩阵

$V(F)_n\xrightarrow{f}U(F)_s$，$n,s$ 分别为对应的维数。

$V$ 的一组基 $\alpha_1,\alpha_2,\dots,\alpha_n$

$U$ 的一组基 $\beta_1,\beta_2,\dots,\beta_s$
$$
\forall \alpha=\sum\limits_{i=1}^nx_i\alpha_i\in V,\ 有f(\alpha)=\sum\limits_{i=1}^nx_if(\alpha_i)\in W\\
\begin{cases}
f(\alpha_1)=a_{11}\beta_1+a_{21}\beta_2+\dots+a_{s1}\beta_s,\\
f(\alpha_2)=a_{12}\beta_1+a_{22}\beta_2+\dots+a_{s2}\beta_s,\\
\vdots\\
f(\alpha_n)=a_{1n}\beta_1+a_{2n}\beta_2+\dots+a_{sn}\beta_s,\\
\end{cases}\\
f(\alpha_1,\alpha_2,\dots,\alpha_n)=\left(f(\alpha_1),f(\alpha_2),\dots,f(\alpha_n)\right)=(\beta_1,\beta_2,\dots,\beta_s)
\left[\begin{matrix}
a_{11} & a_{12} & \cdots & a_{1n} \\
a_{21} & a_{22} & \cdots & a_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
a_{s1} & a_{s2} & \cdots & a_{sn} \\
\end{matrix}\right]
$$
记 $A=(a_{ij})_{s\times n}$ 为 $f$ 在 **基偶** $\left\{\alpha_i\right\}_1^n$ 和 $\left\{\beta_i\right\}_1^s$ 下的 **矩阵**， 特别地，$U=V,\beta_i=\alpha_i$ 时，有：
$$
f(\alpha_1,\alpha_2,\dots,\alpha_n)=\left(f(\alpha_1),f(\alpha_2),\dots,f(\alpha_n)\right)=(\alpha_1,\alpha_2,\dots,\alpha_n)
\left[\begin{matrix}
a_{11} & a_{12} & \cdots & a_{1n} \\
a_{21} & a_{22} & \cdots & a_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
a_{n1} & a_{n2} & \cdots & a_{nn} \\
\end{matrix}\right]
$$
若 $\xi\in V_{(\alpha_1,\alpha_2,\dots,\alpha_n)},f(\xi)\in U_{(\beta_1,\beta_2,\dots,\beta_n)}$ 其坐标分别为 $X,Y$，有 $Y=AX$， $A$​ 就是上述的矩阵。

设$f\in\mathrm{Hom}(V,U)$， $V$ 中两组基满足 $(\beta_1,\beta_2,\dots,\beta_n)=(\alpha_1,\alpha_2,\dots,\alpha_n)P$ ， $W$ 中两组基满足 $(\eta_1,\eta_2,\dots,\eta_s)=(\xi_1,\xi_2,\dots,\xi_s)Q$ 。$f$ 在基偶 $\left\{\alpha_i\right\}_1^n,\left\{\xi_i\right\}_1^s$ 的矩阵为 $A$ ，在基偶 $\left\{\beta_i\right\}_1^n,\left\{\eta_i\right\}_1^s$ 下的矩阵为 $B$ ，则 $B=Q^{-1}AP$ ；

如果上述 $U=V,\xi=\alpha,\eta=\beta$ 那么有：$B=P^{-1}AP$​ ，这就是矩阵 **相似** 的不同定义。

## 不变子空间

设 $f\in\mathrm{Hom}(V,V),W\leq V$ ，若 $\forall\alpha\in W\rightarrow f(\alpha)\in W$ ，则称 $W$ 为 $V$ 关于 $f$ 的 **不变子空间** 。简称为 $f$ 的不变子空间

# 值域和核

## 定义

设 $f\in\mathrm{Home}(V,U)$ ，称 $f(V)$ 为 $f$ 的 **值域** ，记为 $R(f)$ ；

$K(f)=\left\{\alpha\big|f(\alpha)=\mathbf{0}_U,\alpha\in V\right\}$ 为 $f$​ 的 **核** 。

扩展到由于线性变换可以写成矩阵形式，记矩阵为 $A$ 则对应的值域和核可以写成 $R(A)=R(f),K(A)=K(f)$ 

## 维数定理

$f\in\mathrm{Hom}(V,U)$ 且 $V$ 是有限维，那么有： $\dim{R(f)}+\dim{K(f)}=\dim{V}$​

## 求基

设 $A$ 为线性映射矩阵，那么如何求 $R(A),K(A)$ 的基？

值域的基：$A$ 中列向量组的 **极大线性无关组**

核的基：$Ax=0$ 的 **基础解系**

# 符号记法

-   集合，数域，元素，向量：本文中
    -   集合一般指的是某个线性空间
    -   数域指的是定义集合的数域，往往是 实数 或者 复数
    -   元素指的是集合中的某个元素
    -   向量指的是本文探讨的线性空间中的元素，因为本文主要探讨矩阵，所以，元素一般是向量
-   $R,C$：分别表示 **实数** 和 **复数**
-   $F^n,F^{s\times n},F[x],F_n[x]$：分别表示为数域`F`上的 **n维向量** 、 **s行n列的矩阵**  、 **多项式** 、 **最高次数小于n的多项式**
-   $E_{ij}$ ：表示一个矩阵中第 $i$ 行 $j$ 列为1，其他都为0的矩阵。