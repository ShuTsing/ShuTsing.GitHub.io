---
layout:     post
title:      "CQUPT 2018 寒假训练 解题报告 Div1 1.27 - 1.28"
subtitle:   "CQUPT Training Div1 Jan27th - Jan28th 2018"
date:       2018-01-27 14:40:00
author:     "ShuTsing"
header-img: "img/post-bg-26.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## A - The Necklace
### 题目大意
有一串项链如图：

要求上一个珠子的左半部分和下一个珠子的右半部分颜色相同，现在给你n(1<=n<=1000)个珠子，每个珠子都要用上，问能不能构造出满足要求的项链。项链颜色不超过50种。

### 解题思路
很容易想到，将每种颜色作为一个结点，将珠子本身看成是边。首先看这个图是否是连通图，如果不是的话肯定没有办法构造出题目要求的项链。而题目的要求的项链的实际上是一个欧拉回路，这个问题便是问这张图是否是欧拉图，如果是，求出欧拉回路。

需要注意的是，这道题的数据有重边，我一开始在对求欧拉回路的二维数组上加边时直接是赋值为1，减边也是直接赋值为0，WA了好几发。

### 代码

```cpp
#include <iostream>

#include <vector>

#include <cstring>

#include <algorithm>

#include <set>

#define MAXN 55

using namespace std;
    struct edge
    {
        int u;
        int v;
    };
    vector <int> pic[MAXN];
    set <int> nod;
    vector <edge> ans;
    int eul[MAXN][MAXN];
    bool vis[MAXN];
    int n;
void dfs(int u)
{
    vis[u] = 1;
    for(auto& i: pic[u])
        if(!vis[i])
            dfs(i);
    return;
}
void euler(int u)
{
    for(auto& v: nod)
        if (eul[u][v])
        {
            eul[u][v]--;
            eul[v][u]--;
            euler(v);
            ans.push_back({v, u});
        }
    return;
}
void init()
{
    for(auto& i: pic)
        i.clear();
    ans.clear();
    nod.clear();
    memset(vis, 0, sizeof(vis));
    memset(eul, 0, sizeof(eul));
    return;
}
int main(void)
{
    int t;
    cin>>t;
    for(int cse=1; cse<=t; cse++)
    {
        init();
        cin>>n;
        for(int i=0; i<n; i++)
        {
            int x,y;
            cin>>x>>y;
            pic[x].push_back(y);
            pic[y].push_back(x);
            eul[x][y]++;
            eul[y][x]++;
            nod.insert({x, y});
        }
        dfs(*nod.begin());
        euler(*nod.begin());
        bool flag = true;
        for(auto& i: nod)
            if(vis[i] == 0 || pic[i].size()%2 == 1)
            {
                flag = false;
                break;
            }
        cout<<"Case #"<<cse<<endl;
        if(flag == false)
            cout<<"some beads may be lost"<<endl;
        else
            for(auto& i: ans)
                cout<<i.u<<" "<<i.v<<endl;
        if(cse != t)
            cout<<endl;
    }
    return 0;
}

```

## B - Guess

## C - Claw Decomposition

## D - Cells

## E - Puzzle

## F - Knights of the Round Table

## G - Mining Your Own Business

## H - Proving Equivalences

## I - The Largest Clique

## J - Now or later

## K - Astronauts
