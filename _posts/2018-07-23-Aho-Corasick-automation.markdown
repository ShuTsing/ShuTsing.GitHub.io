---
layout:     post
title:      "AC自动机算法讲解"
subtitle:   "Aho-Corasick Automation Algorithm"
date:       2018-07-23 20:32:00
author:     "ShuTsing"
header-img: "img/post-bg-5.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 字符串
---

## 引言

我们在进行字符串匹配的时候，往往会采用时间复杂度O(N+M)的KMP算法，（什么，你不知道KMP？那你凉了啊）。但是如果对于一个多模式字符串匹配问题，即使采用KMP算法，时间复杂度也难以承受（什么，你能承受？那当我没说）。这个时候我们就需要用到优秀的AC自动机算法了。

## 回顾

由于学习AC自动机算法需要前置学习KMP算法和字典树（trie），所以我们先来回顾一下这两个算法和数据结构，并由此引出AC自动机算法。

### KMP单模式串匹配算法

对于字符串匹配问题，我们不难想到一种朴素的O（N*M）时间复杂度的算法，即，将文本串的每个字符都作为开头依次和模式串进行匹配。

1977年的时候，有三位大神忍受不了这种傻逼朴素算法了，于是联手发明了一个优秀的线性时间复杂度的字符串匹配算法，这就是KMP算法。

KMP算法的核心思想是最大限度利用模式串中的重复，出于这个思想，我们需要事先预处理出这个模式串以每一个字符结尾的字符串的相同前后缀的最大长度，这样以来，指向文本串当前匹配位置的指针或者下标变量就不需要回溯，而将模式串向前移动使其前缀和后缀重合，从而达到线性时间复杂度。

### Trie（字典树，前缀树）

Trie树是一种主要用于快速检索，最长前缀匹配等的数据结构，其核心思想是以空间换时间，利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。

Trie树的指针实现的结构为：

```cpp
struct Trie
{
    Trie() : m_wordCnt(0), m_next{NULL} {}
    Trie* m_next[26];   //子节点
    int m_wordCnt;      //以该节点为单词结尾的单词数
};
```

一个典型的Trie如下图：

![1](https://upload-images.jianshu.io/upload_images/13252077-138c71fece60b706.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 正篇

前面提到的KMP算法，要找出最长的相同前后缀，我们一般会用一个next数组实现，假设字符串为：

![2](https://upload-images.jianshu.io/upload_images/13252077-745d75c7de9771a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

则其next数组为：

| IDX | 0 | 1 | 2 | 3 | 4 | 5 | 6
| :-: | :-: | :-: | :-: | :-: | :-: | :-:
| VALUE | -1 | -1 | 0 | 1 | 2 | -1 | 0

如果说，我们不用数组，而改用链表来实现这个功能，那么怎么解决呢？

我们可以用一个指针，将其从最后一个字符，指向前缀中最长相同前后缀的末尾字符，如果最长相同前后缀为0，那么我们在字符串链表启示再插入一个root节点，将其指向root节点，以此来实现跳转：

![3](https://upload-images.jianshu.io/upload_images/13252077-30b0f9efba80464e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个指针有专门的名字，叫做fail指针，中文名叫做失配（~~失去配偶~~ 匹配失败）指针。

通过之前的回顾我们知道，KMP算法是用来对于单模式串匹配的，如果我想一次性对很多模式串都进行匹配怎么办呢？在Trie上搞一遍KMP嘛。

对于AC自动机来说，我们的失配指针就不指向自己的最长相同前后缀的起始字符了，我们将其指向其他字符串的某一个字符。

我们假设其指向字符串str的字符str[i]，那么字符串str[0]...str[i]满足是以当前失配指针所在字符结尾的字符串的一个后缀（好绕口）。

如果没有的话，则跳回Root节点。

这样当当前字符串匹配失败后，能通过失配指针跳到另一个字符串继续进行匹配。

用一张图来说明一下：

//插入图4

这样AC自动机的结构只需在Trie树的结构中加入失配指针即可：

```cpp
struct Trie
{
    Trie()
        : m_wordCnt(0), m_fail(NULL), m_next{NULL} {}
    Trie* m_next[26];   //子节点
    int m_wordCnt;      //以该节点为单词结尾的单词数
    Trie* m_fail;       //失配指针
};
```

那么我们接下来考虑一下，如果建立失配指针。

我们用BFS遍历节点，对于遍历到的当前字符节点C，如果其父亲节点FC的失配指针所指向的节点D存在儿子SD，且儿子节点SD的值和C的值相同，则将字符节点C的失配指针指向SD，如果没有，则从D的失配指针寻找，直到找到root都没有的话，则将其指向root。

代码如下：
```cpp
void AhoCorasick::buildFailer()
{
    while (!m_que.empty())
        m_que.pop();
    m_root->m_fail = NULL;
    m_que.push(m_root);
    while (!m_que.empty())
    {
        Trie* temp = m_que.front();
        m_que.pop();
        Trie* p = NULL;
        for (int i = 0; i < 26; i++)
        {
            if (temp->m_next[i] == NULL)
                continue;
            p = temp->m_fail;
            while (p != NULL)
            {
                if (p->m_next[i] != NULL)
                {
                    temp->m_next[i]->m_fail = p->m_next[i];
                    break;
                }
                p = p->m_fail;
            }
            if (p == NULL)
                temp->m_next[i]->m_fail = m_root;
            m_que.push(temp->m_next[i]);
        }
    }
    return;
}
```

对于字符串的匹配，如果沿着当前路径可以到达目标字符，则走这条路径的下一个节点，否则，跳转到fail指针所指向的节点，直到root节点。

代码如下：

```cpp
int AhoCorasick::query(string& s)
{
    Trie* p = m_root;
    int cnt = 0;
    for (auto& i: s)
    {
        while (p->m_next[i-'a'] == NULL && p != m_root)
            p = p->m_fail;
        p = p->m_next[i-'a'];
        if (p == NULL)
            p = m_root;
        Trie* temp = p;
        while (temp != m_root)
        {
            if (temp->m_wordCnt == -1)
                break;
            cnt+=temp->m_wordCnt;
            temp->m_wordCnt = -1;
            temp = temp->m_fail;
        }
    }
    return cnt;
}
```

一般在算法竞赛中，我们会用数组来模拟指针进行操作，这样一来避免了指针使用的诸多不便，不过使用指针实现更利于理解，所以上述代码均用指针实现。
