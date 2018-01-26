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

## 队列（Queue）

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
