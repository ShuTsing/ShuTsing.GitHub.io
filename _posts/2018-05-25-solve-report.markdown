---
layout:     post
title:      "CQUPT 2018 春季训练 解题报告 5.13 - 5.20"
subtitle:   "CQUPT Training May13th - May20th 2018"
date:       2018-05-25 20:52:00
author:     "ShuTsing"
header-img: "img/post-bg-12.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## C - The Water Problem

**HDU - 5443**

### 题目大意

给出一组数列，求区间最大值。

### 解题思路

本来是sparse-table算法的练习，但是这道题的数据水到了连线段树都不需要，直接n^2就行了。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <cstdio>
#include <algorithm>
using namespace std;
    int n,q,l,r;
    int num[1005];
int main()
{
    int t;
    scanf("%d",&t);
    while(t--)
    {
        scanf("%d",&n);
        for (int i=1;i<=n;i++)
            scanf("%d",&num[i]);
        scanf("%d",&q);
        while(q--)
        {
            scanf("%d%d",&l,&r);
            int ans=-1;
            for (int i=l;i<=r;i++)
                ans=max(ans,num[i]);
            printf("%d\n",ans);
        }
    }
    return 0;
}
```

## G - Assignment

**HDU - 5289**

### 题目大意

给出一组n个数的数列，问满足最大值-最小值的差小于k的区间的个数。

### 解题思路

最大最小值用线段树求，然后枚举左端点，尺取法求右端点。

### 代码

写这道题时异常无聊，顺便练下C++模板的编写方式，就用类模板和指针写了个线段树容器类……

```cpp
#include <iostream>
#include <vector>
#include <cstdio>
#include <algorithm>
#include <limits>
#include <cstring>
using namespace std;
template <typename Var>
class Max
{
public:
    Var operator () (Var& a, Var& b) const
    {
        return a > b ? a : b;
    }
};
template <typename Var>
class Min
{
public:
    Var operator () (Var& a, Var& b) const
    {
        return a < b ? a : b;
    }
};
template <typename Var, Var Init, typename ReFunc>
class SegmentTree
{
private:
    struct node
    {
        node(Var _val): val(_val)
        {
            ls=NULL;
            rs=NULL;
        }
        Var val;
        node* ls;
        node* rs;
    };
    node* root;
    int length;
public:
    SegmentTree(): root(NULL) {}
    SegmentTree(int _length): length(_length), root(NULL) {}
public:
    void update(Var value, int idx);
    Var query(int left, int right);
    void clear();
    void setLength(int _length) {length = _length;}
private:
    void m_update(node*& p, Var value, int idx, int l, int r);
    Var m_query(node*& p, int left, int right, int l, int r);
    void m_clear(node*& p);

};
template <typename Var, Var Init, typename ReFunc>
void SegmentTree <Var, Init, ReFunc>:: clear()
{
    m_clear(root);
    return;
}
template <typename Var, Var Init, typename ReFunc>
void SegmentTree <Var, Init, ReFunc>:: update(Var value, int idx)
{
    if (idx < 0 || idx >= length)
        throw "Access Violation";
    m_update(root, value, idx + 1, 1, length);
    return;
}
template <typename Var, Var Init, typename ReFunc>
Var SegmentTree <Var, Init, ReFunc>:: query(int left, int right)
{
    if (left < 0 || right >= length || left > right)
        throw "Access Violation";
    return (Var)m_query(root, left + 1, right + 1, 1, length);
}
template <typename Var, Var Init, typename ReFunc>
void SegmentTree <Var, Init, ReFunc>:: m_clear(node*& p)
{
    if (p == NULL)
        return;
    if (p->ls != NULL)
        m_clear(p->ls);
    if (p->rs != NULL)
        m_clear(p->rs);
    delete p;
    p=NULL;
}
template <typename Var, Var Init, typename ReFunc>
void SegmentTree <Var, Init, ReFunc>:: m_update(node*& p, Var value, int idx, int l, int r)
{
    if (p == NULL)
        p = new node(Init);
    p->val = ReFunc()(p->val, value);
    if (l == r)
        return;
    int mid = (l + r) >> 1;
    if (idx > mid)
        m_update(p->rs, value, idx, mid + 1, r);
    else
        m_update(p->ls, value, idx, l, mid);
    return;
}
template <typename Var, Var Init, typename ReFunc>
Var SegmentTree <Var, Init, ReFunc>:: m_query(node*& p, int left, int right, int l, int r)
{
    if (p == NULL)
        return Init;
    if (left <= l && r <= right)
        return p->val;
    int mid = (l + r) >> 1;
    Var v1 = Init, v2 = Init;
    if (left <= mid)
        v1 = m_query(p->ls, left, right, l, mid);
    if (right > mid)
        v2 = m_query(p->rs, left, right, mid + 1, r);
    return ReFunc()(v1, v2);
}
int main()
{
    SegmentTree <int, 0, Max <int> > maxt;
    SegmentTree <int, numeric_limits <int>::max(), Min <int> > mint;
    int t;
    scanf("%d",&t);
    while (t--)
    {
        int n,k;
        scanf("%d%d",&n,&k);
        maxt.clear();
        maxt.setLength(n);
        mint.clear();
        mint.setLength(n);
        int num;
        for (int i = 0; i< n;i++)
        {
            scanf("%d",&num);
            maxt.update(num, i);
            mint.update(num, i);
        }
        long long ans = 0;
        int l=0;
        for (int i=0;i<n;i++)
        {
            int maxn = maxt.query(l, i);
            int minn = mint.query(l, i);
            while (maxn-minn>=k&&l<i)
            {//cout<<l<<" "<<i<<" "<<maxn<<" "<<minn<<endl;
                l++;
                maxn = maxt.query(l, i);
                minn = mint.query(l, i);
            }
            ans+=i-l+1;
        }
        printf("%lld\n",ans);
        //cout<<ans<<endl;
    }
    return 0;
}

```

## K - Bombing

**HDU - 4022**

### 题目大意

一个二维矩阵，每次轰炸其中一列或一行，问每次能轰炸掉多少个点。

### 解题思路

平衡树的树套树，用STL自带的平衡树容器类写就行了。

### 代码

```cpp
#include <iostream>
#include <cstdio>
#include <set>
#include <map>
using namespace std;
    map <int, multiset <int> > mpx, mpy;
int main()
{
    int n,m;
    while(scanf("%d%d", &n, &m)!=EOF)
    {
        if(n==0&&m==0)
            break;
        mpx.clear();
        mpy.clear();
        int x, y;
        while(n--)
        {
            scanf("%d%d",&x, &y);
            mpx[x].insert(y);
            mpy[y].insert(x);
        }
        int c, d;
        while(m--)
        {
            scanf("%d%d",&c, &d);
            if (c==0)
            {
                printf("%d\n", mpx[d].size());
                for (auto i: mpx[d])
                    mpy[i].erase(d);
                mpx.erase(d);
            }
            else
            {
                printf("%d\n", mpy[d].size());
                for (auto i: mpy[d])
                    mpx[i].erase(d);
                mpy.erase(d);
            }
        }
        putchar('\n');
    }
    return 0;
}

```
