---
layout:     post
title:      "ACM-ICPC 模板"
subtitle:   "Templates of ACM-ICPC"
date:       2018-01-31 11:24:00
author:     "ShuTsing"
header-img: "img/post-bg-24.jpg"
header-mask: 0.3
catalog:    true
tags:
    - STL
    - 算法
    - 数据结构
    - C++
---

## 图论模板

### 欧拉回路

判断是否是欧拉图：所有结点的度数为偶数。

```cpp
    struct edge
    {
        int u;
        int v;
    };
    vector <edge> ans;
    int eul[MAXN][MAXN];
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
```

### 最短路径

#### Dijkstra 邻接表+路径还原

```cpp
using P = pair <int, int>;
    struct edge
    {
        int to, cost;
    };
    int V;
    vector <edge> G[MAXN];
    int d[MAXN];
    int path[MAXN];
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
                path[e.to] = v;
                que.push(P(d[e.to], e.to));
            }
        }
    }
    return;
}
```

### 双连通分量（BCC）

无向图

```cpp
    struct edge
    {
        int u;
        int v;
    };
    /*pic: 图， bcc: 每个BCC包含的点，从1开始*/
    vector <int> pic[MAXN], bcc[MAXN];
    stack <edge> st;
    /*对于非连通图，需要每个点判断pre调用dfs
      iscut: 判断是否是割点
      bccno：每个点所在的BCC，割点的无意义*/
    int pre[MAXN], iscut[MAXN], bccno[MAXN];
    edge cnt[MAXN];
    /*bcc_cnt: BCC数量，从1开始*/
    int dfs_clock, bcc_cnt;
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
```
