---
title: A星算法
id: 99
date: 2024-12-03 17:09:58
auther: yutanbird
cover: 
excerpt: 从BFS出发，介绍了一下Astar算法
permalink: /archives/a%E6%98%9F%E7%AE%97%E6%B3%95
categories:
 - 算法
tags: 
 - c
 - astar
---





# BFS（广度优先）寻路算法

## 简单介绍

BFS寻路算法可以寻找到A->B的一条最短路径，而且从点A出发，可以寻找任何一点到A的最短路径，地图中的每个节点最多会被访问一次，最坏的时间复杂度应该是地图上的节点数。

## 过程详解

假设是寻找A->B的最短路径

- 一开始：将节点A加入到队列中
- 重复步骤：
    - 取出队列第一个节点M，依次把节点M的下一步可达的节点加入到队列中（这些节点要求未被访问过，且是可达点）
    - 为这些点的父亲设置成M
- 等到找到B点，就可以跳出循环，从B点依次回溯，根据父节点来回溯。

## 代码详解

```c++
class MyPoint{
public:
    int x, y;  // x,y左边
    MyPoint *father; // 父节点
    int distance; // 距离一开始的距离
    MyPoint();
    MyPoint(int x, int y);
    MyPoint(int x, int y, MyPoint *father, int distance);
    MyPoint operator+(const MyPoint &p1, const MyPoint &p2);
    bool operator==(const MyPoint &p1, const MyPoint &p2);
    bool operator!=(const MyPoint &p1, const MyPoint &p2);
    bool valid();
    bool can_run();
};
// 规定x向下为正，y向右为正
const int M=200, N = 200;  // 定义地图的大小
const int UP = 0, DOWN = 1, LEFT = 2, RIGHT = 3;  // 定义上下左右
const std::vector<int> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};  // 定义上下左右走一步，应该如何更新坐标

// 定义一个点类
MyPoint::MyPoint():x(-1),y(-1),father(nullptr),distance(-1){
    
}
MyPoint::MyPoint(int x, int y):x(x),y(y),father(nullptr),distance(-1){
    
}
MyPoint::MyPoint(int x, int y, MyPoint *father, int distance):x(x),y(y),father(father),distance(distance){
    
}
MyPoint MyPoint::operator+(const MyPoint &p1, const MyPoint &p2){
    MyPoint ans(p1.x+p2.x, p1.y+p2.y);
    return ans;
}
bool MyPoint::operator==(const MyPoint &p1, const MyPoint &p2){
    return p1.x == p2.x and p1.y == p2.y;
}
bool MyPoint::operator!=(const MyPoint &p1, const MyPoint &p2){
    return p1.x != p2.x or p1.y != p2.y;
}
bool MyPoint::valid(){
    return x >= 0 and x < M and y >= 0 and y < N;
}
bool MyPoint::can_run(){
	// 定义该点是否在地图上不可达
}

/**
 * @biref: 获取开始节点start到末尾节点end的最短路径，假设只能走上下左右
 * @param: start 开始节点，注意保证有效性
 * @param: end 末尾节点，注意保证有效性
 * @return: 返回一条从start到end的最短路径，如果不可达，就返回空
**/
std::vector<MyPoint> bfs(MyPoint start, MyPoint end){
    std::vector<MyPoint> sol; // 定义路径数组
    std::queue<MyPoint> q;    // 定义广度优先队列
    q.push(start);            // 初始start
    start.father = nullptr;   // start的父亲为空
    start.distance = 0;       // start的距离为0
    std::vector<std::vector<MyPoint>> map(M, std::vector<MyPoint>(N));  // 把遍历过的点都需要保存
    std::vector<std::vector<bool>> vis(M, std::vector<bool>(N, false)); // 定义某个点是否被访问过
    map[start.x][start.y] = start;  // 初始化地图的start
    while(not q.empty()){
        MyPoint cur = q.front();  // 获取队列中的第一个点
        if(cur == end) break;     // 如果已经找到末尾节点，就可以退出了
        q.pop();                  // 删除队列的第一个点
        std::vector<int> dir = {0, 1, 2, 3};  
        std::shuffle(dir.begin(), dir.end(), std::mt19937(std::random_device()()));  // 随机顺序访问
        for(int d:dir){
            auto np = cur + dirs[d];  // 下一个点
            if(not np.valid() or np.can_run()) continue;  // 如果这个点不可达或者无效，那就不访问
            if(vis[np.x][np.y]) continue; // 已经访问过了
            vis[np.x][np.y] = true;  // 设定已经访问过了
            np.father = &cur;  // 设定父亲节点为当前节点
            np.distance = cur.distance + 1;
            map[np.x][np.y] = np;     // 保存下来
            q.push(map[np.x][np.y]);  // 把下一个点加入到队列
        }
    }
    if(cur != end) return sol; // start -> end 没有路径
    // 从终点回溯
    while(cur != start){
        sol.push_back(cur);
        cur = *(cur.father);
    }
    sol.push_back(start);
    std::reverse(sol.begin(), sol.end());  // 翻转路径，因为是回溯的时候是从end开始
    return sol;
}

```



---

# Astar（A星，A\*）算法

## 简单介绍

A\*算法是迪杰斯特拉算法的一个扩展，与BFS相比，多了一个启发式函数，所以A\*算法也是一种启发式搜索。常用的启发式函数一般是，到当前节点的实际花费加上到终点的预估花费，这些花费用曼哈顿距离就行。

## 过程详解

假设从A->B

- 一开始：将节点A加入到队列中
- 重复步骤：
    - 当前节点为M，依次把节点M的下一步可达节点加入到队列，但是需要根据启发式函数的大小顺序加入。（这些节点同样要求可达且未访问过的）
    - 将这些节点的父亲设置为M
- 重复直到找到B

## 过程详解

```c++
class MyPoint{
public:
    int x, y;  // x,y左边
    MyPoint *father; // 父节点
    int distance; // 距离一开始的距离
    MyPoint();
    MyPoint(int x, int y);
    MyPoint(int x, int y, MyPoint *father, int distance);
    MyPoint operator+(const MyPoint &p1, const MyPoint &p2);
    bool operator==(const MyPoint &p1, const MyPoint &p2);
    bool operator!=(const MyPoint &p1, const MyPoint &p2);
    bool valid();
    bool can_run();
    int manhattan(const Point &p);
};

// 规定x向下为正，y向右为正
const int M=200, N = 200;  // 定义地图的大小
const int UP = 0, DOWN = 1, LEFT = 2, RIGHT = 3;  // 定义上下左右
const std::vector<int> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};  // 定义上下左右走一步，应该如何更新坐标

// 定义一个点类
MyPoint::MyPoint():x(-1),y(-1),father(nullptr),distance(-1){
    
}
MyPoint::MyPoint(int x, int y):x(x),y(y),father(nullptr),distance(-1){
    
}
MyPoint::MyPoint(int x, int y, MyPoint *father, int distance):x(x),y(y),father(father),distance(distance){
    
}
MyPoint MyPoint::operator+(const MyPoint &p1, const MyPoint &p2){
    MyPoint ans(p1.x+p2.x, p1.y+p2.y);
    return ans;
}
bool MyPoint::operator==(const MyPoint &p1, const MyPoint &p2){
    return p1.x == p2.x and p1.y == p2.y;
}
bool MyPoint::operator!=(const MyPoint &p1, const MyPoint &p2){
    return p1.x != p2.x or p1.y != p2.y;
}
bool MyPoint::valid(){
    return x >= 0 and x < M and y >= 0 and y < N;
}
bool MyPoint::can_run(){
	// 定义该点是否在地图上不可达
}
int MyPoint::manhattan(const Point &p){
    return std::abs(p.x - this->x) + std::abs(p.y - this->y);
}

/**
 * @biref: 获取开始节点start到末尾节点end的最短路径，假设只能走上下左右
 * @param: start 开始节点，注意保证有效性
 * @param: end 末尾节点，注意保证有效性
 * @return: 返回一条从start到end的最短路径，如果不可达，就返回空
**/
std::vector<MyPoint> bfs(MyPoint start, MyPoint end){
    struct cmp{
      bool operator()(const MyPoint &p1, const MyPoint &p2){
          // 定义启发式函数
          // f = g + h，g是距离起点的代价，h是距离终点的预估代价(用曼哈顿来估计，或者曼哈顿与欧氏距离带权和)
          int g1 = p1.distance, g2 = p2.distance;
          int h1 = p1.manhattan(end), h2 = p2.manhattan(end);
          return g1 + h1 < g2 + h2;
      }  
    };
    std::vector<MyPoint> sol; // 定义路径数组
    std::priority_queue<MyPoint, std::vector<MyPoint>, cmp> q;    // 定义广度优先队列
    q.push(start);            // 初始start
    start.father = nullptr;   // start的父亲为空
    start.distance = 0;       // start的距离为0
    std::vector<std::vector<MyPoint>> map(M, std::vector<MyPoint>(N));  // 把遍历过的点都需要保存
    std::vector<std::vector<bool>> vis(M, std::vector<bool>(N, false)); // 定义某个点是否被访问过
    map[start.x][start.y] = start;  // 初始化地图的start

    while(not q.empty()){
        MyPoint cur = q.front();  // 获取队列中的第一个点
        if(cur == end) break;     // 如果已经找到末尾节点，就可以退出了
        q.pop();                  // 删除队列的第一个点
        std::vector<int> dir = {0, 1, 2, 3};  
        for(int d:dir){
            auto np = cur + dirs[d];  // 下一个点
            if(not np.valid() or np.can_run()) continue;  // 如果这个点不可达或者无效，那就不访问
            if(vis[np.x][np.y]) continue; // 已经访问过了
            vis[np.x][np.y] = true;  // 设定已经访问过了
            np.father = &cur;  // 设定父亲节点为当前节点
            np.distance = cur.distance + 1;
            map[np.x][np.y] = np;     // 保存下来
            q.push(map[np.x][np.y]);  // 把下一个点加入到队列
        }
    }
    if(cur != end) return sol; // start -> end 没有路径
    // 从终点回溯
    while(cur != start){
        sol.push_back(cur);
        cur = *(cur.father);
    }
    sol.push_back(start);
    std::reverse(sol.begin(), sol.end());  // 翻转路径，因为是回溯的时候是从end开始
    return sol;
}
```

&emsp;当然，这个跟其他帖子说的少了`open_list`和`close_list`，`open_list`已经用优先队列`priority_queue`替代了，然后访问过的节点状态依旧使用`vis`来维持，因此没有用`open_list`以及`close_list`了。

&emsp;上述代码与BFS的区别只在于：`std::priority_queue<MyPoint, std::vector<MyPoint>, cmp> q;`优先替代了简单的队列`queue`，优先队列的优先级，根据`struct cmp;`来计算。

---

# Dijkstra算法

## 简单介绍



## 过程详解



## 代码详解