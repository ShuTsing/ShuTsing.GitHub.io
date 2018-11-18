---
layout:     post
title:      "协会上课——基本数据结构"
subtitle:   "Basic Data Structure"
date:       2018-11-18 05:12:00
author:     "ShuTsing"
header-img: "img/post-bg-36.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
---

## 写在前面

逻辑结构的种类：  

- 集合 结构中的数据元素除了同属于一种类型外，别无其它关系。
- 线性结构 数据元素之间一对一的关系
- 树形结构 数据元素之间一对多的关系
- 图状结构或网状结构 结构中的数据元素之间存在多对多的关系

![front](https://upload-images.jianshu.io/upload_images/13252077-198c09fe61c87049.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 栈（Stack）

栈 是仅允许在表尾进行插入和删除操作的线性表。我们把允许插入和删除的一端称为栈顶（top），另一端称为栈底（bottom）。栈是一种后进先出（Last In First Out）的线性表，简称（LIFO)结构。栈的一个典型应用是在集合中保存元素的同时颠倒元素的相对顺序。

![Stack](https://upload-images.jianshu.io/upload_images/13252077-f6917e708a7c1fb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Stack2](https://upload-images.jianshu.io/upload_images/2959073-1e166da9b9a83a09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

我们来看点例题：  

### 火车出栈

**问题描述**

有N辆火车，以序列1方式进站，判断是否能以序列2方式出栈，可以中途出站。

如果能，输出火车站的进出站记录。

![pic1](https://upload-images.jianshu.io/upload_images/13252077-9cf361fd21243de6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**样例输入**

3 123 321   
7 1234567 4321576    

**样例输出**

Yes.  
in in in out out out   
FINISH  
Yes.  
in in in in out out out out in out in in out out  
FINISH  

**解题思路**

每次先判断栈顶元素如果出栈能否满足序列2，如果能，则栈顶元素出栈，反之，序列1中剩下元素的第一个入栈。


## 队列（Queue）

队列 是只允许在一端进行插入操作，在另一端进行删除操作的线性表。它是一种基于先进先出（First In First Out，简称FIFO）策略的集合类型。允许插入的一端称为队尾，允许删除的一端称为队头。

![Queue](https://upload-images.jianshu.io/upload_images/2959073-63edc783dc0dfab1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

与栈不同的是，队列元素的出列是在队头，即下表为0的位置。为保证队头不为空，每次出队后队列中的所有元素都得向前移动，此时时间复杂度为 O(n)。此时队列的实现和线性表的顺序存储结构完全相同，不在详述。

若不限制队列的元素必须存储在数组的前n个单元，出队的性能就能大大提高。但这种结构可能产生「假溢出」现象，即数组末尾元素已被占用，如果继续向后就会产生下标越界，而前面为空。如下图：

![Queue2](https://upload-images.jianshu.io/upload_images/2959073-712c4a842466da3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/542)

而我们一般会用一种叫做循环队列的方式来解决假溢出：  

![Queue3](https://upload-images.jianshu.io/upload_images/13252077-dba523e5f3b63e27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们也可以用链式存储结构解决假溢出问题。

![Queue4](https://upload-images.jianshu.io/upload_images/2959073-4792802b936f2bb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

循环队列和链式存储的队列的基本操作的时间复杂度都为O(1)。

来一道例题：

### 士兵队列训练

**问题描述**

某部队进行新兵队列训练，将新兵从一开始按顺序依次编号，并排成一行横队，训练的规则如下：从头开始一至二报数，凡报到二的出列，剩下的向小序号方向靠拢，再从头开始进行一至三报数，凡报到三的出列，剩下的向小序号方向靠拢，继续从头开始进行一至二报数。。。，以后从头开始轮流进行一至二报数、一至三报数直到剩下的人数不超过三人为止。   

第一行为组数N，接着为N行新兵人数，新兵人数不超过5000。  

共有N行，分别对应输入的新兵人数，每行输出剩下的新兵最初的编号，编号之间有一个空格。

**样例输入**

2 20 40

**样例输出**

1 7 19 1 19 37

**解题思路**

先将所有人入队，然后对于每次循环，如果是1，2循环，是1的出队后再入队，如果是1，2，3循环，1，2的出队后再入队，直到某轮循环结束后，队列中不超过三人。

## 树

![tree](https://upload-images.jianshu.io/upload_images/13252077-0b57e45763225fc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 树的基本术语：

- 孩子结点：结点的子树的根称为该结点的孩子；   
- 双亲结点：B结点是A结点的孩子，则A结点是B结点的双亲；   
- 兄弟结点：同一双亲的孩子结点；   
- 树的高（深）度：树中最大的结点层；   
- 结点的度：结点子树的个数；   
- 叶子结点：也叫终端结点，是度为0的结点；    
- 森林：互不相交的树集合；   
- 边权：边上的权值；
- 点权：点上的权值；

### 树的储存方式

- 双亲表示法
- 图的储存方式

#### 双亲表示法

Father[u] = v;    
表示节点v的双亲节点为u。    

![hahaha](https://upload-images.jianshu.io/upload_images/13252077-f27da36c87462134.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 图

![graph](https://upload-images.jianshu.io/upload_images/13252077-9d5e37f00f072a09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 图的术语

- 有向图：图的所有边单向可达；
- 无向图：图的所有边双向可达；
- 路径：依次遍历顶点序列之间的边所形成的轨迹。没有重复顶点的路径称为简单路径；
- 环：环是指路径包含相同的顶点两次或两次以上。也就是说，在有向图的一条路径中，如果从某顶点出发，最后能够返回该顶点，则该路径是环。除了第一个顶点和最后一个顶点之外，其余顶点不重复出现的回路称为简单环或简单回路；
- 生成树：一个连通图的生成树是一个极小连通子图，它含有图中全部顶点，但只有足以构成一棵树的n-1条边；
- 连通性：对于无向图而言，如果它的每个顶点都能通过某条路径到达其他顶点，那么我们称它为连通的。如果该条件在有向图中同样成立，则称该图是强连通。

### 图的储存方式

- 邻接矩阵
- 邻接表
- 边集数组

### 邻接矩阵

![233](https://upload-images.jianshu.io/upload_images/13252077-9ade787751490f40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 邻接表
![gg](https://upload-images.jianshu.io/upload_images/13252077-7358c670b72c4cae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![gg2](https://upload-images.jianshu.io/upload_images/13252077-ef4bd428365853d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 图的遍历

#### 深度优先遍历

![dep](https://upload-images.jianshu.io/upload_images/13252077-6f069d3bff9849f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 宽度优先遍历

![wth](https://upload-images.jianshu.io/upload_images/13252077-bec0cde574bcd744.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
