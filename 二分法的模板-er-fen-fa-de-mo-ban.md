---
title: 二分法的模板
date: 2023-11-22 22:01:52.866
updated: 2023-11-22 22:01:52.866
url: /archives/er-fen-fa-de-mo-ban
categories: 
- 编程学习
tags: 
- 二分法
---

# 二分法模板

假设vec从小到大

```c++
template<T> int binarySearch(vector<T> &vec, T &target){
    int left = 0, right = vec.size();
    
    while(left < right){
        int mid = (left + right) / 2;
        if(vec[mid] < left){
            left = mid+1;
        }else if (vec[mid] == left){
            return mid;
        }else {
            right = mid-1;
        }
    }
    return vec[left] == target ? left : -1;
}
```

把这部分代码，分成若干部分来分析

## 第一部分

```c++
while(left < right){
    ...
}

return vec[left] == target ? left : -1;
```

有些可以写作`left <= right`，本身这两种也没有什么区别，只在最后的`return`取值有区别，也就是循环终止条件不一样

## 第二部分

```c++
int mid = (left + right) / 2;

// 以下写法都是等价的
int mid = (left + right) >> 1;
int mid = left + (right -  left) / 2;
int mid = left + ((right - left) >> 1);

// 后两种不会超过int类型最大值，假设你的数组长度比较大，那么就得用后面两种，python除外
```

## 第三部分

```c++
if(vec[mid] < target){
    // how to do
}else if(vec[mid] == target){
    // how to do
}else {
    // how to do
}
```

这一部分就需要根据自己实际的需求来，下面说的指针均表示`mid`指针

- 第一行：如果指针指向的 **值** 小于 **目标** ；或者说指针需要向右移动的条件
- 第二行：如果指针指向的 **值** 等于 **目标**；或者说指针已经找到目标了
- 第三行：如果指针指向的 **值** 大于 **目标**；或者说指针需要向左移动的条件

