---
layout:     post
title:      "CQUPT 2018 寒假训练 解题报告 Div1 1.30 - 1.31"
subtitle:   "CQUPT Training Div1 Jan30th - Jan31th 2018"
date:       2018-02-13 21:58:00
author:     "ShuTsing"
header-img: "img/post-bg-27.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## A - Airport Express

**UVA - 11374**

### 题目大意

有经济线和商务线两种列车路线，现在手上只有一张能坐一站的商务列车票，给出经济线和商务线车站，路线，求起点到终点的最短距离和路程。

### 解题思路

先求出只做经济线的起点到所有点的最短路ds[n]和终点到所有点的最短路dt[n]，枚举每条商业线(u, v)，然后将该商业线正反接较小的一个和Dmin(s, t)比较。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>
#include <list>
#define INF 10000000
#define MAXN 505
using namespace std;
using P = pair <int, int>;
    struct edge
    {
        int to, cost;
    };
    int V;
    vector <edge> G[MAXN];
    int d[MAXN], ds[MAXN];
    int path[MAXN];
    list <int> ansp;
void dijkstra(int s)
{
    priority_queue <P, vector <P>, greater<P> > que;
    fill(d, d + V, INF);
    memset(path, -1, sizeof(path));
    d[s] = 0;
    que.push(P(0, s));
    while(!que.empty())
    {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (d[v] < p.first)
            continue;
        for (auto& i: G[v])
        {
            edge e = i;
            if (d[e.to] > d[v] + e.cost)
            {
                d[e.to] = d[v] + e.cost;
                path[e.to] = v;
                que.push(P(d[e.to], e.to));
            }
        }
    }
    return;
}
int main(void)
{
    bool flag=false;
    int n, s, e;
    while(cin>>n>>s>>e)
    {
        for(auto& i: G)
            i.clear();
        ansp.clear();
        int m;
        cin>>m;
        for(int i=0;i<m;i++)
        {
            int u,v,c;
            cin>>u>>v>>c;
            G[u].push_back({v, c});
            G[v].push_back({u, c});
        }
        V=n+1;
        dijkstra(s);
        for(int i=1;i<=n;i++)
            ds[i]=d[i];
        dijkstra(e);
        int k;
        cin>>k;
        int minu, minv, minc, mincc=INF;
        for(int i=0;i<k;i++)
        {
            int u,v,c;
            cin>>u>>v>>c;
            int yc=INF;
            for (auto& j: G[u])
                if(j.to==v)
                    yc=min(yc, j.cost);
            if(c<yc)
            {//cout<<ds[u]+d[v]+c<<endl;
                if(ds[u]+d[v]+c<mincc)
                {
                    minu=u;
                    minv=v;
                    minc=c;
                    mincc=ds[u]+d[v]+c;
                }
                if(ds[v]+d[u]+c<mincc)
                {
                    minu=v;
                    minv=u;
                    minc=c;
                    mincc=ds[v]+d[u]+c;
                }
            }
        }
        if(mincc<ds[e])
            G[minu].push_back({minv, minc});
        dijkstra(s);
        if(flag)
            cout<<endl;
        flag=true;
        for(int i=e;i!=-1;i=path[i])
            ansp.push_front(i);
        cout<<ansp.front();
        ansp.pop_front();
        for(auto& i: ansp)
            cout<<" "<<i;
        cout<<endl;
        if(mincc<ds[e])
            cout<<minu<<endl;
        else
            cout<<"Ticket Not Used"<<endl;
        cout<<d[e]<<endl;
    }
    return 0;
}

```

## B - Walk Through the Forest

**UVA - 10917**

### 题目大意

有一个人要穿过一个森林，起点为1，终点为2，这个人比较扯，他走的每一条路(u, v)都满足存在一条从v出发回家的路，比所有从u出发回家的路径都短。

计算有多少条回家路径。

### 解题思路

反向求最短路，得到每个点到终点的最短路，然后dp或者记忆化搜索，dp[i]表示存在多少条从i到终点的路径。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>
#include <list>
#include <bitset>
#define INF 10000000
#define MAXN 1005
using namespace std;
using P = pair <int, int>;
    struct edge
    {
        int to, cost;
    };
    int V;
    vector <edge> G[MAXN];
    int d[MAXN],ans;
    int path[MAXN];
    list <int> ansp;
    bitset <1005> bs[1005];
    bool vis[MAXN];
    int dp[MAXN];
void dijkstra(int s)
{
    priority_queue <P, vector <P>, greater<P> > que;
    fill(d, d + V + 1, INF);
    memset(path, -1, sizeof(path));
    d[s] = 0;
    que.push(P(0, s));
    while(!que.empty())
    {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (d[v] < p.first)
            continue;
        for (auto& i: G[v])
        {
            edge e = i;
            if (d[e.to] > d[v] + e.cost)
            {
                d[e.to] = d[v] + e.cost;
                path[e.to] = v;//cout<<endl;
                que.push(P(d[e.to], e.to));
            }
        }
    }
    return;
}
int dfs(int s)
{
    if(s==2)
        return dp[s]=1;
    if(dp[s]!=-1)
        return dp[s];

    int re=0;
    for(auto& i: G[s])
        if(d[s]>d[i.to])
            re += dfs(i.to);
    return dp[s]=re;
}
int main()
{
    int e;
    while(cin>>V)
    {ans=0;
        if(V==0)
            break;
        cin>>e;
        memset(vis,0,sizeof(vis));
        memset(dp,-1,sizeof(dp));
        for(auto& i: G)
            i.clear();
        while(e--)
        {
            int u,v,c;
            cin>>u>>v>>c;
            G[u].push_back({v,c});
            G[v].push_back({u,c});
            bs[u][v]=bs[v][u]=true;
        }
        dijkstra(2);
        cout<<dfs(1)<<endl;;
    }
    return 0;
}

```

## C - Warfare And Logistics

**UVA - 1416**

### 题目大意

一个地方有n个结点m条路，现在要选择轰炸一条路，使得每对结点的最短路径权值（运输代价）之和最大，如果两个结点不连通，边权值（运输代价）视为L。

### 解题思路

还好没有出现不连通的运输代价L比连通的还小的奇葩数据，不然我的代码就A不了了……

最短路树，如果某条边不在最短路树上面，删除对于运输代价之和也没有影响，所以只需要遍历在最短路树上面的边。

注意有重边。(我搞了个巨复杂的map来解决重边233)

### 代码

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>
#include <algorithm>
#include <map>
#include <numeric>
#define INF 10000000
#define MAXN 105
using namespace std;
using P = pair <int, int>;
    struct edge
    {
        int to, cost;
        bool operator == (const edge& x) const
        {
            return to == x.to && cost == x.cost;
        }
        bool operator != (const edge& x) const
        {
            return to != x.to || cost != x.cost;
        }
    };
    int V;
    vector <edge> G[MAXN];
    int d[MAXN], ds[MAXN];
    int path[MAXN][MAXN], idx;
    map <pair <int, int>, int> mp;
    int dc;
void dijkstra(int s)
{
    priority_queue <P, vector <P>, greater<P> > que;
    fill(d, d + V + 1, INF);

    d[s] = 0;
    que.push(P(0, s));
    while(!que.empty())
    {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (d[v] < p.first)
            continue;
        for (auto& i: G[v])
        {
            edge e = i;
            if (d[e.to] > d[v] + e.cost)
            {
                d[e.to] = d[v] + e.cost;
                path[idx][e.to] = v;
                que.push(P(d[e.to], e.to));
            }
        }
    }
    return;
}

void dijkstra2(int s)
{
    priority_queue <P, vector <P>, greater<P> > que;
    fill(d, d + V + 1, INF);
    d[s] = 0;
    que.push(P(0, s));
    while(!que.empty())
    {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (d[v] < p.first)
            continue;
        for (int i=0;i<G[v].size();i++)
        {
            edge e = G[v][i];
            if (d[e.to] > d[v] + e.cost)
            {
                d[e.to] = d[v] + e.cost;
                que.push(P(d[e.to], e.to));
            }
        }
    }
    return;
}

int main()
{
    int n,m,l;
    while(cin>>n>>m>>l)
    {
        V=n;
        mp.clear();
        memset(path, -1, sizeof(path));
        for(auto& i: G)
            i.clear();
        dc=0;
        for(int i=0;i<m;i++)
        {
            int u,v,c;
            cin>>u>>v>>c;

            auto itr=mp.find({max(u, v), min(u, v)});
            if(itr == mp.end())
                mp[{max(u, v), min(u, v)}] = c;
            else
                itr->second=min(itr->second, c);

            G[u].push_back({v,c});
            G[v].push_back({u,c});
        }
        for(idx=1;idx<=n;idx++)
        {
            int sum1=0;
            dijkstra(idx);
            for(int i=1;i<=n;i++)
                sum1 += (d[i]==INF?l:d[i]);
            ds[idx]=sum1;
        }
        int ans1=accumulate(ds+1, ds+n+1, 0);
        for(auto& i: mp)
        {
            int sum2=0;
            for(int id=1;id<=n;id++)
            {
                if(path[id][i.first.second]==i.first.first || path[id][i.first.first]==i.first.second)
                {
                    edge x,y;
                    for(auto& j: G[i.first.first])
                        if(j==(edge){i.first.second, i.second})
                        {
                            swap(j, G[i.first.first].back());
                            x=G[i.first.first].back();
                            G[i.first.first].pop_back();
                            break;
                        }
                    for(auto& j: G[i.first.second])
                        if(j==(edge){i.first.first, i.second})
                        {
                            swap(j, G[i.first.second].back());
                            y=G[i.first.second].back();
                            G[i.first.second].pop_back();
                            break;
                        }
                    dijkstra2(id);
                    for(int k=1;k<=n;k++)
                        sum2 += (d[k]==INF?l:d[k]);

                    sum2-=ds[id];

                    G[i.first.first].push_back(x);
                    G[i.first.second].push_back(y);
                }

            }
            dc=max(sum2, dc);
        }
        cout<<ans1<<" "<<ans1+dc<<endl;
    }
    return 0;
}

```

## D - The Toll! Revisited

**UVA - 10537**

### 题目大意

给出两种节点，大写字母节点经过时交当前货物的%5的费用，通过小写节点时交数值为1的费用，求起点到终点的最少费用，费用相同的节点取字典序小的。

### 解题思路

这道题的字典序是指的ASCII值，贼坑。

反向求最短路，逆推，这样可以解决大写字母节点的费用问题。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>
#include <list>
#define INF 10000000000
#define MAXN 233
using namespace std;
using P = pair <int, int>;
using ll = long long;
    struct edge
    {
        int to, cost;
    };
    int V;
    vector <edge> G[MAXN];
    ll d[MAXN];
    int path[MAXN];
    ll ans;
void dijkstra(int s)
{
    priority_queue <P, vector <P>, greater<P> > que;
    fill(d, d + 200, INF);
    memset(path, -1, sizeof(path));
    d[s] = ans;
    que.push(P(0, s));
    while(!que.empty())
    {
        P p = que.top();
        que.pop();
        int v = p.second;
        if (d[v] < p.first)
            continue;
        for (auto& i: G[v])
        {
            edge e = i;
            if (v>=97)
            {
                if(d[e.to] > d[v] + e.cost)
                {
                    d[e.to] = d[v] + e.cost;
                    path[e.to] = v;
                    que.push(P(d[e.to], e.to));
                }
                else if(d[e.to] == d[v] + e.cost&&path[e.to]>v)
                {
                    d[e.to] = d[v] + e.cost;
                    path[e.to] = v;
                    que.push(P(d[e.to], e.to));
                }


            }
            else if(v<97)
            {
                if(d[e.to]>d[v]+(d[v]-1)/19+1)
                {
                    d[e.to] = d[v]+(d[v]-1)/19+1;
                    path[e.to] = v;
                    que.push(P(d[e.to], e.to));
                }
                else if(d[e.to]==d[v]+(d[v]-1)/19+1&&path[e.to]>v)
                {
                    d[e.to] = d[v]+(d[v]-1)/19+1;
                    path[e.to] = v;
                    que.push(P(d[e.to], e.to));
                }
            }
        }
    }
    return;
}
int main(void)
{
    int n;
    int cse=1;
    while(cin>>n)
    {
        if(n==-1)
            break;
        for(auto& i: G)
            i.clear();
        V=n;
        for(int i=0;i<n;i++)
        {
            char u, v;
            cin>>u>>v;
            G[u].push_back({v, 1});
            G[v].push_back({u, 1});
        }
        char s, t;
        cin>>ans>>s>>t;
        dijkstra(t);
        ans=d[s];
        cout<<"Case "<<cse<<":"<<endl;
        cout<<ans<<endl;
        for(int i=s;i!=-1;i=path[i],cout<<(i==-1?"":"-"))
            cout<<(char)i;
        cout<<endl;
        cse++;
    }
    return 0;
}

```
