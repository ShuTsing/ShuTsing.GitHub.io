---
layout:     post
title:      "简单数据结构"
subtitle:   "Simple Data Structure"
date:       2018-01-14 12:40:00
author:     "ShuTsing"
header-img: "img/post-bg-19.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - C++
---

接下来会介绍栈，队列这两种线性数据结构。

## 栈（Stack）

栈 是仅允许在表尾进行插入和删除操作的线性表。我们把允许插入和删除的一端称为栈顶（top），另一端称为栈底（bottom）。栈是一种后进先出（Last In First Out）的线性表，简称（LIFO)结构。栈的一个典型应用是在集合中保存元素的同时颠倒元素的相对顺序。

![Stack](https://upload-images.jianshu.io/upload_images/13252077-f6917e708a7c1fb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Stack2](https://upload-images.jianshu.io/upload_images/2959073-1e166da9b9a83a09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)


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

## 例题

我们来看点例题：  

### 火车出栈

**问题描述**

有N辆火车，以序列1方式进站，判断是否能以序列2方式出栈，可以中途出站。

如果能，输出火车站的进出站记录。

![pic1](http://acm.hdu.edu.cn/data/images/1022-1.jpg)
![pic1](http://acm.hdu.edu.cn/data/images/1022-2.jpg)
![pic1](http://acm.hdu.edu.cn/data/images/1022-3.jpg)

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
