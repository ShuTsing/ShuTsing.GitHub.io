---
layout:     post
title:      "CQUPT 2018 寒假训练 解题报告 Div1 1.28 - 1.29"
subtitle:   "CQUPT Training Div1 Jan28th - Jan29th 2018"
date:       2018-02-12 22:30:00
author:     "ShuTsing"
header-img: "img/post-bg-28.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## A - Doves and bombs

**UVA - 10765**

### 题目大意

给一个n个点的无向连通图，现给定某点的鸽子值为去掉该点后的连通块个数，输出鸽子值前m大的点。

### 解题思路

先求出这个图的所有双连通分量，然后看每个点在多少个双连通分量里面，从多到少排序m个。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <stack>
#include <vector>
#include <algorithm>
#define MAXN 10005
using namespace std;
    struct edge
    {
        int u;
        int v;
    };
    vector <int> pic[MAXN], bcc[MAXN];
    stack <edge> st;
    int pre[MAXN], iscut[MAXN], bccno[MAXN];
    edge cnt[MAXN];
    int n, m, dfs_clock, bcc_cnt;
int dfs(int u, int fa)
{
    int lowu = pre[u] = ++dfs_clock;
    int child = 0;
    for(auto& i: pic[u])
    {
        int v = i;
        edge e = {u, v};
        if(!pre[v])
        {
            st.push(e);
            child++;
            int lowv = dfs(v, u);
            lowu = min(lowu, lowv);
            if(lowv >= pre[u])
            {
                iscut[u] = true;
                bcc_cnt++;
                bcc[bcc_cnt].clear();
                while(true)
                {
                    edge x = st.top();
                    st.pop();
                    if(bccno[x.u] != bcc_cnt)
                    {
                        bcc[bcc_cnt].push_back(x.u);
                        bccno[x.u] = bcc_cnt;
                    }
                    if(bccno[x.v] != bcc_cnt)
                    {
                        bcc[bcc_cnt].push_back(x.v);
                        bccno[x.v] = bcc_cnt;
                    }
                    if(x.u==u && x.v==v)
                        break;
                }
            }
        }
        else if(pre[v]<pre[u] && v!=fa)
        {
            st.push(e);
            lowu = min(lowu, pre[v]);
        }
    }
    if(fa<0 && child==1)
        iscut[u] = 0;
    return lowu;
}
void init()
{
    for(auto& i: pic)
        i.clear();
    for(auto& i: bcc)
        i.clear();
    while(!st.empty())
        st.pop();
    memset(pre, 0, sizeof(pre));
    memset(cnt, 0, sizeof(cnt));
    memset(iscut, 0, sizeof(iscut));
    memset(bccno, 0, sizeof(bccno));
    for(int i=0; i<MAXN; i++)
        cnt[i].u = i;
    dfs_clock = bcc_cnt = 0;
    return;
}
int main()
{
    while(cin>>n>>m)
    {
        if(n==0 && m==0)
            break;
        init();
        int u, v;
        while(cin>>u>>v)
        {
            if(u==-1 && v==-1)
                break;
            pic[u].push_back(v);
            pic[v].push_back(u);
        }
        for(int i=0; i<n; i++)
            if(!pre[i])
                dfs(i, -1);
        for(int i=1; i<=bcc_cnt; i++)
            for(auto& j: bcc[i])
                cnt[j].v++;//for(int i=0;i<n;i++) cout<<" "<<cnt[i].v<<endl;
        for(int i=0; i<n; i++)
            if(!iscut[i])
                cnt[i].v = 1;

        sort(cnt, cnt + n, [](edge a, edge b){return a.v > b.v || a.v == b.v && a.u < b.u;});
        for(int i=0; i<m; i++)
            cout<<cnt[i].u<<" "<<cnt[i].v<<endl;
        cout<<endl;
    }
    return 0;
}

```

## B - Wedding

**UVA - 11294**

### 题目大意

**原题目有部分不健全的描述，已经被和谐。**

有n对夫妻，其中n-1对夫妻是受邀参加第一对夫妻的婚礼的，新郎和新娘分别坐桌子的两侧，其他的夫妻也不能坐在同一侧，同时，还有m对人有点那啥的关系，每对有那啥关系的人不能都坐在新娘对面。问怎么去安排座位。

### 解题思路

2-SAT

夫妻既不能都在新娘一侧，也不能都在新郎一侧，而那啥关系的一对只用满足不同在新郎一侧，假设每个人有两种状态，0表示在新郎一侧，1表示在新娘一侧。

那么对于夫妻a和b来说：  
a or b  
!a or !b

对于那啥关系的a和b来说：
a or b

基于以上来建图，用2-SAT求解即可。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <cstring>
#define MAXN 233
using namespace std;
struct TwoSAT
{
    int n;
    vector <int> G[MAXN * 2];
    bool mark[MAXN * 2];
    int S[MAXN * 2], c;

    void init(int n)
    {
        this->n = n;
        for (int i = 0; i < n*2; i++)
            G[i].clear();
        memset(mark, 0, sizeof(mark));
        return;
    }

    bool dfs(int x)
    {
        if (mark[x^1])
            return false;
        if (mark[x])
            return true;
        mark[x] = true;
        S[c++] = x;
        for (auto& i: G[x])
            if(!dfs(i))
                return false;
        return true;
    }

    void add_clause(int x, int xval, int y, int yval)
    {
        x = x*2 + xval;
        y = y*2 + yval;
        G[x^1].push_back(y);
        G[y^1].push_back(x);
    }

    bool solve()
    {
        for (int i = 0; i < n*2; i += 2)
            if (!mark[i] && !mark[i + 1])
            {
                c = 0;
                if (!dfs(i))
                {
                    while (c > 0)
                        mark[S[--c]] = false;
                    if(!dfs(i + 1))
                        return false;
                }
            }
        return true;
    }
};
    TwoSAT ts;
int main()
{
    string a, b;
    int n, m;
    while (cin>>n>>m)
    {
        if(n==0 && m==0)
            break;
        ts.init(n);
        while (m--)
        {
            cin>>a>>b;
            int av = (a.back() == 'h') ? 0 : 1;
            int bv = (b.back() == 'h') ? 0 : 1;
            a.pop_back();
            b.pop_back();
            ts.add_clause(stoi(a), av, stoi(b), bv);
        }
        ts.G[0].push_back(1);
        if (!ts.solve())
            cout<< "bad luck";
        else
            for (int i = 2; i < 2*n; i += 2, cout<< ((i < 2*n) ? " " : ""))
                if (ts.mark[i] == ts.mark[1])
                    cout<< i/2<< "h";
                else
                    cout<< i/2<< "w";
        cout<< endl;
    }
    return 0;
}

```
