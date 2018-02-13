---
layout:     post
title:      "CQUPT 2018 寒假训练 解题报告 Div1 1.31 - 2.01"
subtitle:   "CQUPT Training Div1 Jan31th - Feb01st 2018"
date:       2018-02-13 23:38:00
author:     "ShuTsing"
header-img: "img/post-bg-23.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## A - Fill

**UVA - 10603**

### 题目大意

有三个杯子它们的容量分别是a,b,c, 并且初始状态下第一个和第二个是空的， 第三个杯子是满水的。可以把一个杯子的水倒入另一个杯子，当然，当被倒的杯子满了或者倒的杯子水完了，就不能继续倒了。

最终要求出能倒出的小于等于d且最接近d的水量，输出该种情况的最小倒水量

### 解题思路

高中做过倒水问题，当时是求最少倒水次数，就没认真读题，求的水量是小于等于d的最接近d的，把小于等于也看漏了，wa了很久……

转化为图论的最短路径问题，将当前状态和能实现的下一状态连边，求最短路。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <queue>
#define INF 1000000
#define DE 62
using namespace std;
    int rl[205][205];
    pair <int, int> path[205][205];
    int a, b, c, d;
    int gud[3];
    struct zt
    {
        int x, y, cost;
        bool operator > (const zt& a) const
        {
            return cost > a.cost;
        }

        bool operator < (const zt& a) const
        {
            return cost < a.cost;
        }
    };
void bfs(int x, int y)
{
    for (int i = 0; i <= a; i++)
        fill(rl[i], rl[i] + b + 1, INF);
    priority_queue <zt, vector <zt>, greater<zt> > q;
    rl[x][y] = 0;
    q.push({x, y, 0});
    while (!q.empty())
    {
        zt p = q.top();
        q.pop();
        if (rl[p.x][p.y] < p.cost)
            continue;
        for (int i = 0; i < 3; i++)
            for (int j = 0; j < 3; j++)
            {
                if (i == j)
                    continue;
                int temp[3] = {p.x, p.y, c - p.x - p.y};
                if (temp[j] != gud[j] && temp[i])
                {
                    int dao = min(temp[i], gud[j] - temp[j]);
                    temp[i] -= dao;
                    temp[j] += dao;
                    if (rl[temp[0]][temp[1]] > rl[p.x][p.y] + dao)
                    {
                        rl[temp[0]][temp[1]] = rl[p.x][p.y] + dao;
                        q.push({temp[0], temp[1], rl[temp[0]][temp[1]]});
                        path[temp[0]][temp[1]]={p.x, p.y};
                    }
                }
            }
    }
    return;
}
int main(void)
{
    int t;
    cin>> t;
    while (t--)
    {
        cin>> a>> b>> c>> d;
        gud[0] = a;
        gud[1] = b;
        gud[2] = c;
        for(int i=0;i<=a;i++)
            for(int j=0;j<=b;j++)
                path[i][j]={-1,-1};
        bfs(0, 0);
        int dis = -1, val = INF, ans1;
        for (int i = 0; i <= a; i++)
            for (int j = 0; j <= b; j++)
            {
                if (rl[i][j] == INF)
                    continue;
                if (i > d && j > d && c - i - j > d)
                    continue;

                int t = -1;
                if (j > t && j <= d)
                    t = j;
                if (i > t && i <= d)
                    t = i;
                if (c - i - j > t && c - i - j <= d)
                    t = c - i - j;

                if (dis > t)
                    continue;
                else if (dis == t)
                {
                    if (val > rl[i][j])
                        val = rl[i][j];
                }
                else
                {
                    dis = t;
                    val = rl[i][j];
                }
            }
        cout<< val<< " "<< dis<< endl;
    }
    return 0;
}

```

## D - Flying to Fredericton

**UVA - 11280**

### 题目大意

给出n个城市的m条航线的机票价格，从城市Calgary到终点城市Fredericton，可以转机，询问当最大停留次数为S时的最少价钱。

### 解题思路

用SPFA算法可以求解，加一个数组d[i][j]表示到顶点i，步数为j步的最短路径。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <vector>
#include <queue>
#include <algorithm>
#include <string>
#include <map>
#define MAXN 105
#define INF 100000000
using namespace std;
    struct node
    {
        int to, cost, time;
    };
    vector <node> G[MAXN];
    int d[MAXN][MAXN];
    bool vis[MAXN][MAXN];
    int V;
    map <string, int> mp;
bool spfa(int s, int ed)
{
    queue <pair<int, int> > q;
    memset(vis, 0, sizeof(vis));
    d[s][0] = 0;
    q.push({s, 0});
    while (!q.empty())
    {
        auto u = q.front();
        q.pop();
        vis[u.first][u.second] = false;
        for (auto& i: G[u.first])
        {
            int v = i.to;
            int c = i.cost;
            if (d[v][u.second+1] > d[u.first][u.second] + c)
            {
                d[v][u.second+1] = d[u.first][u.second] + c;
                if (!vis[v][u.second+1])
                {
                    q.push({v, u.second+1});
                    vis[v][u.second+1] = true;
                }
            }
        }
    }
    return true;
}
int main(void)
{
    int t;
    int cse = 1;
    cin>>t;
    while(t--)
    {
        cout<<"Scenario #"<<cse<<endl;
        mp.clear();
        for(auto& i: G)
            i.clear();
        int n;
        cin>>n;
        V = n;
        string s, s2;
        for(int i=0; i<n; i++)
        {
            cin>>s;
            mp[s] = i;
        }
        int m;
        cin>>m;
        while(m--)
        {
            int cost;
            cin>>s>>s2>>cost;
            G[mp[s]].push_back({mp[s2], cost});
        }
        for(auto& i: d)
            fill(i, i + V + 3, INF);
        spfa(0, n-1);
        int q;
        cin>>q;
        while(q--)
        {
            int num;
            cin>>num;
            num=min(num, n-1);
            int ans = INF;
            for(int i=0;i<=num+1;i++)
                ans=min(ans,d[n-1][i]);
            if(ans == INF)
                cout<<"No satisfactory flights"<<endl;
            else
                cout<<"Total cost of flight(s) is $"<<ans<<endl;
        }
        cse++;
        if(t!=0)
            cout<<endl;
    }
    return 0;
}

```
