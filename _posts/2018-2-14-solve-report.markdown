---
layout:     post
title:      "CQUPT 2018 寒假训练 解题报告 Div1 2.02 - 2.03"
subtitle:   "CQUPT Training Div1 Feb02nd - Feb03rd 2018"
date:       2018-02-14 00:27:00
author:     "ShuTsing"
header-img: "img/post-bg-20.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## A - Qin Shi Huang's National Road System

**UVALive - 5713**

### 题目大意

有n个城市，给出坐标，秦始皇打算修一些路使得任意两个城市都能连通。徐福可以免费帮秦始皇修一条路。设徐福的法术造的路两端的城市人口和为 A，B为人工造道路长度之和，A尽量大，B尽量小，求出最大的 A/B

### 解题思路

类似于次小生成树，先求出最小生成树，枚举每条边，加上成环再删去环的最小瓶颈路。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <cstdio>
#define MAXN 1005
#define INF 10000000
using namespace std;
    double cost[MAXN][MAXN];
    double mincost[MAXN];
    bool used[MAXN];
    bool tree[MAXN][MAXN];
    double maxph[MAXN][MAXN];
    int pre[MAXN];
    int V;
double prim(int s)
{
    fill(mincost, mincost + V + 1, INF);
    memset(used, 0, sizeof(used));
    memset(tree, 0, sizeof(tree));
    memset(pre, -1, sizeof(pre));
    for (int i=0;i<=V;i++)
        fill(maxph[i], maxph[i]+V+1, -INF);
    mincost[s] = 0;
    double res = 0;
    while (true)
    {
        int v = -1;
        for (int u = 1; u <= V; u++)
            if (!used[u] && (v == -1 || mincost[u] < mincost[v]))
                v = u;
        if (v == -1)
            break;
        used[v] = true;
        if (pre[v] != -1)
            tree[pre[v]][v] = true;
        res += mincost[v];
        int finu = INF;
        for (int u = 1; u <= V; u++)
        {
            if (used[u]&&u!=v)
                maxph[u][v] = maxph[v][u] = max(mincost[v], maxph[u][pre[v]]);
            if (mincost[u] > cost[v][u])
            {
                mincost[u] = cost[v][u];
                pre[u] = v;
            }
        }

    }
    return res;
}
    struct city
    {
        int x;
        int y;
        int p;
    };
    city cit[MAXN];

double smst(int s, double res)
{

    double ans = -INF;
    for (int i = 1; i <= V; i++)
        for (int j = i+1; j <= V; j++)
                ans=max(ans, (double)(cit[i].p+cit[j].p)/(res-maxph[i][j]));
    return ans;
}
int main()
{
    int t;
    cin>>t;
    while (t--)
    {
        cin>>V;
        for (int i=0;i<=V;i++)
        {
            fill(cost[i], cost[i] +V+1, INF);
        }
        for(int i=1;i<=V;i++)
        {
            int u,v,c;
            cin>>u>>v>>c;
            cit[i]={u,v,c};
        }
        for(int i=1;i<=V;i++)
        {
            for(int j=1;j<=V;j++)
            {
                if(i==j)
                    continue;
                cost[i][j]=cost[j][i]
                    =min(cost[j][i], sqrt((cit[i].x-cit[j].x)*(cit[i].x-cit[j].x)+(cit[i].y-cit[j].y)*(cit[i].y-cit[j].y)));
            }
        }
        double res = prim(1);//cout<<res<<endl;
        double ans = smst(1, res);
        printf("%.2lf\n", ans);
        //cout<<"  "<<ans<<endl;
    }
    return 0;
}

```

## E - ACM Contest and Blackout

**UVA - 10600**

### 题目大意

给出n个点和m条边的值，求出最小生成树和次小生成树的值。

### 解题思路

这题的数据范围非常的小，求次小生成树都不需要什么加边成环再删边，直接遍历删除最小生成树上的边再求一次最小生成树就好。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <set>
#define MAXN 105
#define INF 100000000
using namespace std;
    set <int> cost[MAXN][MAXN];
    int mincost[MAXN];
    bool used[MAXN];
    bool tree[MAXN][MAXN];
    int pre[MAXN];
    int V;
int prim(int s)
{
    fill(mincost, mincost + V + 1, INF);
    memset(used, 0, sizeof(used));
    memset(tree, 0, sizeof(tree));
    memset(pre, -1, sizeof(pre));
    mincost[s] = 0;//cout<<"  "<<mincost[0]<<endl;
    int res = 0;
    while (true)
    {//cout<<pre[4]<<endl;
        int v = -1;
        for (int u = 1; u <= V; u++)
            if (!used[u] && (v == -1 || mincost[u] < mincost[v]))
                v = u;
        if (v == -1)
            break;
        used[v] = true;
        if (pre[v] != -1)
            tree[pre[v]][v] = true;
        res += mincost[v];//cout<<"ahaha "<<pre[v]<<" "<<v<<" "<<mincost[v]<<endl;
        int finu = INF;
        for (int u = 1; u <= V; u++)
            if(mincost[u] > *cost[v][u].begin())
            {
                mincost[u] = * cost[v][u].begin();
                pre[u] = v;
            }
    }
    return res;
}
    int mintr[MAXN][MAXN], ans;
int main()
{
    int t;
    cin>>t;
    while (t--)
    {
        int m;
        cin>>V>>m;
        for(int i=0;i<=V;i++)
            for(int j=0;j<=V;j++)
            {
                cost[i][j].clear();
                cost[i][j].insert(INF);
            }

        ans = INF;
        while(m--)
        {
            int u,v,c;
            cin>>u>>v>>c;
            cost[u][v].insert(c);
            cost[v][u].insert(c);
        }
        int ans1=prim(1);
        for(int i=1;i<=V;i++)for(int j=1;j<=V;j++)mintr[i][j]=tree[i][j];
        for(int i=1;i<=V;i++)
        {
            for(int j=1;j<=V;j++)
            {
                if(mintr[i][j] == 1)
                {
                    int c=* cost[i][j].begin();
                    cost[i][j].erase(c);
                    cost[j][i].erase(c);
                    int temp=prim(1);
                    if(temp >= ans1)
                        ans = min(ans, temp);
                    cost[i][j].insert(c);
                    cost[j][i].insert(c);
                }
            }
        }
        cout<<ans1<<" "<<ans<<endl;
    }
    return 0;
}


```
