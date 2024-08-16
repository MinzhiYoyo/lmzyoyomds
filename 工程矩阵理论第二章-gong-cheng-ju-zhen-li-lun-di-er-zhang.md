---
title: 工程矩阵理论第二章
date: 2024-06-09 20:39:03.378
updated: 2024-06-09 20:39:03.378
url: /archives/gong-cheng-ju-zhen-li-lun-di-er-zhang
categories: 
- 数学
tags: 
- 数学
---

# 内积空间

## 概念与定义

设 $V$ 为数域 $F(R\ \mathrm{or}\ C)$ 上的线性空间，若有一法则使 $V$ 中任意两个向量 $\alpha,\beta$ 确定为 $F$ 中唯一的数，记为 $\langle\alpha,\beta\rangle$ ，且满足：

1.   $\langle\alpha,\beta\rangle=\overline{\langle\beta,\alpha\rangle}$ ：共轭相等，实数域相等，复数域共轭；
2.   $\langle\alpha+\beta,\gamma\rangle=\langle\alpha,\gamma\rangle+\langle\beta,\gamma\rangle,\quad\langle\alpha,\beta+\gamma\rangle=\langle\alpha,\beta\rangle+\langle \alpha,\gamma\rangle,\forall\alpha,\beta,\gamma\in V$ ；
3.   $\langle k\alpha,\beta\rangle=k\langle\alpha,\beta\rangle,\forall k\in F,\quad\langle \alpha,k\beta\rangle=\overline{k}\langle\alpha,\beta\rangle,\forall k\in F\forall\alpha,\beta\in F$ ；
4.   $\langle\alpha,\alpha\rangle\geq0,\quad\langle\alpha,\theta\rangle=\langle\theta,\alpha\rangle=0$ 第一个式子当且仅当 $\alpha=\mathbf{0}$ 等号成立

称 $\langle\alpha,\beta\rangle$ 为 **内积** ， $V$ 为 **内积空间** ；细分下 $V(C),V(R)$​ 分别称为 **酉空间** 和 **欧式空间** 。

酉矩阵定义：$A^HA=I\quad(A^H=\overline{A}^T)$ ，称 $A$ 为酉矩阵

## 运算

酉空间中： $\lang X,Y\rang=\overline{Y}^TX$ ，记 $Y^H=\overline{Y}^T$ 

欧氏空间： $\lang X,Y\rang=Y^TX$​

## 度量矩阵

$\alpha_1,\alpha_2,\dots,\alpha_n$ 为内积空间的一组基，记 $\lang\alpha_i,\alpha_j\rang=g_{ij}\quad(i,j=1,2,\dots,n)$ 称 $G=(g_{ij})$ 为该基的 **度量矩阵** ，设内积空间两个向量为 $\alpha,\beta$ ，在该基下的坐标为 $X,Y$ ，有： $\lang\alpha,\beta\rang=X^TG\overline(Y)=Y^HG^TX$ 

## 内积的定理

1.   内积的长度： $\|\alpha\|=\sqrt{\lang\alpha,\alpha\rang}$ ；
2.   $|\langle\alpha,\beta\rangle|\leq\langle\alpha,\alpha\rangle\langle\beta,\beta\rangle$ ，等号成立充要条件为两个向量线性相关
3.   $\|\alpha+\beta\|\leq\|\alpha\|+\|\beta\|$
4.   向量的距离：$\mathrm{d}(\alpha,\beta)=\|\alpha-\beta\|$ ，有 $\mathrm{d}(\alpha,\beta)=\mathrm{d}(\beta,\alpha),\mathrm{d}(\alpha,\beta)\leq\mathrm{d}(\alpha,\gamma)+\mathrm{d}(\gamma,\beta)$ ；
5.   向量的夹角： $\cos\phi=\frac{\langle\alpha,\beta\rangle}{\|\alpha\|\cdot\|\beta\|}$ 
6.   向量正交： $\langle\alpha,\beta\rangle=0$ 记为 $\alpha\bot\beta$ ，内积空间中相互正交的一组基叫做 **正交基** ，如果每个向量是单位向量则为 **标准正交基** 

# Schmidt正交法

记一组基为 $\alpha_1,\alpha_2,\dots,\alpha_n$ ，下面演示 `Shemidt` 正交法的步骤
$$
\beta_1=\alpha_1\\
\beta_2=\alpha_2-\frac{\lang\alpha_2,\beta_1\rang}{\lang\beta_1,\beta_1\rang}\beta_1\\
\vdots\\
\beta_i=\alpha_i - \sum\limits_{j=1}^{i-1}\frac{\lang\alpha_i,\beta_j\rang}{\lang\beta_j,\beta_j\rang}\beta_j,\quad(i=2,3,\dots,n)
$$
经过上面得到 $\beta$ 为正交基，下面进行标准化
$$
\eta_i=\frac{\beta_i}{\|\beta_i\|},\quad(i=1,2,\dots,n)
$$
得到的 $\eta$ 为标准正交基。

回顾上面步骤，我们可以看出，每个 $\alpha$ 都可以由 $\beta$ 表示，进而由 $\eta$ 表示
$$
\begin{cases}
\alpha_1=\eta_1\cdot\|\beta_1\|\\
\alpha_2=\eta_1\cdot\frac{\lang\alpha_2,\beta_1\rang}{\lang\beta_1,\beta_1\rang}\|\beta_1\|+\eta_2\cdot\|\beta_2\|\\
\vdots\\
\alpha_n=\eta_1\cdot\frac{\lang\alpha_n,\beta_1\rang}{\lang\beta_1,\beta_1\rang}\|\beta_1\|+\eta_2\cdot\frac{\lang\alpha_n,\beta_2\rang}{\lang\beta_2,\beta_2\rang}\|\beta_2\|+\dots+\eta_n\cdot\|\beta_n\|\\
\end{cases}
$$
可以写成矩阵形式
$$
A=(\alpha_1,\alpha_2,\dots,\alpha_n)=(\eta_1,\eta_2,\dots,\eta_n)
\left(\begin{matrix}
\|\beta_1\| & \frac{\lang\alpha_2,\beta_1\rang}{\lang\beta_1,\beta_1\rang}\|\beta_1\| & \cdots & \frac{\lang\alpha_n,\beta_1\rang}{\lang\beta_1,\beta_1\rang}\|\beta_1\|\\
0 & \|\beta_2\| & \cdots & \frac{\lang\alpha_n,\beta_2\rang}{\lang\beta_2,\beta_2\rang}\|\beta_2\|\\
\vdots & \vdots & \ddots & \vdots\\
0 & 0 & \cdots & \|\beta_n\|
\end{matrix}\right)
=UT
$$
$U$ 是酉矩阵， $T$ 是主对角非负的上三角矩阵。

# 正交补

正交补： $W^\bot=\left\{\alpha\big|\alpha\bot W,\alpha\in V\right\}$ 为 $W$ 的正交补，其中的每个向量正交于 $W$ 中的每个向量，也称 $W^\bot\bot W$ 

-   $V=W^\bot \oplus W$ 
-   $V=W\oplus U\rightarrow U=W^\bot$ 
-   $\left[K(A)\right]^\bot=R(A^H),[R(A)]^\bot=K(A^H)$
-   $\forall\alpha\in V,\exist惟一\beta\in W,\mathrm{d}(\beta)=\min\limits_{\xi\in W}d(\xi,\alpha)$ 

# 等距变换

## 定义

设 $f$ 为内积空间 $V$ 的线性变换，若 $\lang f(\alpha),f(\beta)\rang=\lang\alpha,\beta\rang\quad(\forall\alpha,\beta\in V)$ ，则称 $f$ 是 **等距变换** 。细分下，酉空间称为 **酉变换** ，欧氏空间称为 **正交变换** 。

若 $f$ 为内积空间 $V$ 的线性变换，下列命题等价：

1.   $\|f(\alpha)\|=\|\alpha\|,\forall\alpha\in V$ ；
2.   $\lang f(\alpha),f(\beta)\rang=\lang\alpha,\beta\rang\quad(\forall\alpha,\beta\in V)$ ；
3.   $f$ 作用在 $V$ 中标准正交基上仍然是标准正交基；
4.   $f$ 在标准正交基下的矩阵是 **酉矩阵**；

## 旋转变换

旋转变换是一种等距变换

设在 $R^2$ 中绕原点旋转 $\theta$ 的变换记为 $f$ ，取正交单位向量 $\mathbf{e_1},\mathbf{e_2}$ 。（$R^2$ 可以看作在平面直角坐标系下， $\mathbf{e_1}=(1,0),\mathbf{e_2}=(0,1)$ ）
$$
f(\mathbf{e_1})=(\cos\theta)\mathbf{e_1}+(\sin\theta)\mathbf{e_2}\\
f(\mathbf{e_2})=(-\sin\theta)\mathbf{e_1}+(\cos\theta)\mathbf{e_2}\\
f=\left[\begin{matrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{matrix}\right]
$$

## 镜像变换

设在 $R^2$ 上， $\mathbf{\omega}$ 为单位向量， $\mathbf{n}$ 为垂直其的单位向量，任意一个向量 $\mathbf{\overset{\rightarrow}{OA}}$ 关于 $\mathbf{n}$ 的镜像为 $\mathbf{\overset{\rightarrow}{OB}}$ ，关于 $\mathbf{\omega}$ 的镜像为 $\mathbf{\overset{\rightarrow}{OC}}$ 。那么我们要求 $\mathbf{\overset{\rightarrow}{OB}}$ 如何求呢？
$$
\mathbf{\overset{\rightarrow}{OA}} + \mathbf{\overset{\rightarrow}{OC}} = 2\cdot\|\mathbf{\overset{\rightarrow}{OA}}\|\cdot\cos\angle{AO\omega}\cdot\mathbf{\omega}\\
\|\mathbf{\overset{\rightarrow}{OA}}\|\cdot\cos\angle{AO\omega}=\frac{\mathbf{\overset{\rightarrow}{OA}}\cdot\mathbf{\omega}}{\|\mathbf{\omega}\|}=\mathbf{\overset{\rightarrow}{OA}}\cdot\mathbf{\omega}\\
\mathbf{\overset{\rightarrow}{OB}} = -\mathbf{\overset{\rightarrow}{OC}}=\mathbf{\overset{\rightarrow}{OA}}-2(\mathbf{\overset{\rightarrow}{OA}}\cdot\mathbf{\omega})\mathbf{\omega}
$$
推广到酉空间，记为 $H$ 
$$
H(X)=X-2\lang X,\omega\rang\omega\quad(\forall X\in C^n)
$$
不妨扩展一下，记 $\omega,\epsilon_2,\epsilon_3,\dots,\epsilon_n$ 为 $C^n$ 的标准正交基，于是有：
$$
H(\omega)=\omega-2\lang\omega,\omega\rang\omega=-\omega\\
H(\epsilon_i)=\epsilon_i-2\lang\epsilon_i,\omega\rang\omega=\epsilon_i\\
H=\left(\begin{matrix}
-1 &   &        &   &\\
  & 1 &        &   &\\
  &   & \ddots &   &\\
  &   &        & 1 &
\end{matrix}\right)=\mathrm{diag}(-1,1,1,\dots,1)
$$


其是一个行列式为1的酉矩阵。