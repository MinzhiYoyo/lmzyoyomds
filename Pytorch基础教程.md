---
title: Pytorch基础教程
date: 2024-04-24 00:05:32.027
updated: 2024-04-24 16:13:43.744
url: /archives/pytorchlearning
categories: 
- 人工智能
tags: 
- pytorch
---

# 前言

本文仅仅作为学习的记录



# 张量

## 简单概念

先从数字开始介绍，如$1, 3.4, 2/3$等等都是成为数字，它是单个，并不由什么元素组成。

在数字的基础上我们定义出了向量，列向量或者行向量都是向量，它具有了维度，是一维的，其由多个数字组成，如$(1,2,3,4,5)$，定义了一个一维向量。

再对此扩展，我们定义了矩阵，它是二维的，其由多个向量组成，如
$$
\left(\begin{matrix}1&2&3\\4&5&6\\7&8&9\end{matrix}\right)
$$
再多扩展一定，我们定义了张量，它是任意维度的，并且它更加广泛，可以是0维的，可以包含0个数等等，又或者它某一维度大小为0等等。

## 用法

```python
import torch
a = torch.tensor([[1,2,3], [4,5,6]])
# 我们构建了一个简单的张量
print(a.shape)  

# 可以打印它的形状，我们会得到：
# torch.Size([2, 3])
```

## 维度

现在需要抛开`tensor`跟一般向量矩阵的关系，不要想想在哪个位置的值是多少，也同样抛开矩阵，数组等的概念，如行啊、列啊等等，因为当维度上去了，已经没法创造那么多词语来表示维度了，只需要记得，维度从0开始，依次是0维，1维等等。`pytorch`中常用`dim`表示维度

下面来介绍集中改变维度的方法

```python
ra = torch.randn(3, 4, 5, 6, 7, 8)
# 上面是生成一个六维的数据，其维度为6，维度编号从0到5
# dim=0时，有3组数据
# dim=1时，有4组数据
# 以此类推
print(ra.shape)
```

维度同样可以升维和降维

```python
# 一维化

# 函数原型
def flatten(input: Tensor, start_dim: _int = 0, end_dim: _int = -1) -> Tensor: 
    # 包含在torch里面
    # 直接torch.flatten()即可
	pass

# 该函数可以将input进行一维化处理，而且支持指定范围的维度，比如
rb = torch.flatten(ra, start_dim=2, end_dim=4)
# 便是讲第2维到第4维的维度一维化
print(rb.shape)
# 得到：torch.Size([3, 4, 210, 8])
# 可以看到 2,3,4维度的数据已经变成一维了，210=5*6*7
```

升一维与降一维

```python
# 升一维，其实是逆压缩一个维度
def unsqueeze(self, dim: _int) -> Tensor: 
    pass
# dim表示维度，传入int型数据即可，在维度为dim的维进行升维
r3_uq = ra.unsqueeze(dim=3)
print(r3_uq.shape) # torch.Size([3, 4, 5, 1, 6, 7, 8])


r_uq = ra.unsqueeze(-1) # 再最后升一维
print(r_uq.shape) # torch.Size([3, 4, 5, 6, 7, 8, 1])


# 降一维，其实是叫做压缩一个维度，也就是压缩最后一个维度
def squeeze(self) -> Tensor:
    pass
# 只有当维度大小为1的时候，才能压缩的
print(ra.squeeze().shape)    # torch.Size([3, 4, 5, 6, 7, 8])，没有变化的
print(r3_uq.squeeze().shape) # torch.Size([3, 4, 5, 6, 7, 8])，第3个维度被压缩了
print(r_uq.squeeze().shape)  # torch.Size([3, 4, 5, 6, 7, 8])，最后一个被压缩了

```

# 数据集加载



# 神经网络搭建



