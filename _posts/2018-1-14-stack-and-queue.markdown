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

![Stack](http://img.blog.csdn.net/20170411174800756?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRXRoYW5fQXBwbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![Stack2](https://upload-images.jianshu.io/upload_images/2959073-1e166da9b9a83a09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

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
