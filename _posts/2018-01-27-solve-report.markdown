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

**UVA 10054**

### 题目大意
有一串项链如图：

![pic](http://byvzy.img48.wal8.com/img48/436694_20140928135133/15171203138.png)

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

**UVALive 4255**

## 题目大意

给出一个矩阵，如果(i, j)为'+'，那么ai到aj的和为正数，'-'为负数，'0'为0。

## 解题思路

可以转化为前缀和问题。用一个数组sum记录前缀和的值，将ai到aj的值转化为sum[j]-sum[i-1]，然后进行拓扑排序。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <vector>
#include <queue>
#include <string>
using namespace std;
    int sum[100];
    vector <int> pic[100];
    bool V[100];
    int N;
    int indeg[100];
void bfs(int s)
{
    queue <int> q;
    for(int i=0;i<=s;i++)
    {
        if(!indeg[i])
        {
            q.push(i);
            V[i]=true;
        }
    }
    while(!q.empty())
    {
        int u=q.front();
        q.pop();
        for(auto& i: pic[u])
        {
            sum[i]=sum[u]-1;
            indeg[i]--;
            if(indeg[i]==0&&!V[i])
            {
                V[i]=true;
                q.push(i);
            }
        }
    }
    return;
}
void init()
{
    memset(V,0,sizeof(V));
    memset(sum,0,sizeof(sum));
    memset(indeg,0,sizeof(indeg));
    for(auto& i:pic)
        i.clear();
    return;
}
int main()
{
    int t;
    cin>>t;
    while(t--)
    {
        init();
        cin>>N;
        int i,j;
        for(i=1;i<=N;i++)
        {
            for(j=i;j<=N;j++)
            {
                char c;
                cin>>c;//cout<<j<<" "<<c<<endl;
                if(c=='+')
                {
                    pic[j].push_back(i-1);
                    indeg[i-1]++;
                }
                else if(c=='-')
                {
                    pic[i-1].push_back(j);
                    indeg[j]++;
                }
            }
        }
        bfs(N);
        for(int i=1;i<=N;i++)
        {
            cout<<sum[i]-sum[i-1];
            if(i^N)
                cout<<" ";
        }
        cout<<endl;
    }
    return 0;
}

```

## C - Claw Decomposition

**UVA 11396**

## 题目大意

给出一个无向图，每个点都和三个点相连形成一个爪，问能不能将图分解成若干的爪子且每条边只属于一个爪子。

## 解题思路

思考一下可以想到满足条件的图是一个二分图，我们接着可以去用二分图性质判定的染色法求解。

也就是一个点染成黑色，相邻的点都染成白色，让相邻的两个点颜色都不一样。

### 代码
```cpp
#include <iostream>
#include <vector>
#include <cstring>
#include <algorithm>
#include <cstdio>
#define MAXN 305
using namespace std;
    vector <int> pic[MAXN];
    int nc[MAXN],vis[MAXN];
    int n;
    bool flag = true;
void dfs(int n)
{
    if(vis[n])
        return;
    vis[n] = 1;
    for(auto i: pic[n])
    {
        if(nc[i] == -1)
            nc[i] = !nc[n];
        if(nc[i] == nc[n])
        {
            flag = false;
            return;
        }
        dfs(i);
    }
    return;
}
int main(void)
{
    while(cin>>n)
    {
        if(n==0)
            return 0;
        memset(nc, -1, sizeof(nc));
        memset(vis, 0, sizeof(vis));
        flag = true;
        for(auto& i: pic)
            i.clear();
        int u,v;
        while(true)
        {
            cin>>u>>v;
            if(u == 0 && v == 0)
                break;
            pic[u].push_back(v);
            pic[v].push_back(u);
        }
        nc[1] = 1;
        dfs(1);
        if(flag)
            cout<<"YES"<<endl;
        else
            cout<<"NO"<<endl;
    }
    return 0;
}

```


## G - Mining Your Own Business

**UVALive 5135**

## 题目大意

n个隧道连接一些点，要在一些点上安装逃生装置，满足不管哪个点发生坍塌，其他点的人都可以安全逃生，求最小逃生装置安装数量和安装的方案数。

## 解题思路

如果这个图只有一个双连通分量，那么就只需要随便两个点安装逃生装置，如果一个双连通分量有两个及以上的割点，那这个连通分量就不需要安装，因为两个割点无法同时坍塌。

最后看哪些双连通分量只有一个割点。

### 代码
```cpp
#include <iostream>
#include <vector>
#include <cstring>
#include <stack>
#define MAXN 50005
using namespace std;
using ll = long long;
    struct edge
    {
        int u;
        int v;
    };
    vector <ll> pic[MAXN], bcc[MAXN];
    stack <edge> st;
    int pre[MAXN], iscut[MAXN], bccno[MAXN];
    int n, dfs_clock, bcc_cnt;
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
    memset(iscut, 0, sizeof(iscut));
    memset(bccno, 0, sizeof(bccno));
    dfs_clock = bcc_cnt = 0;
    return;
}

int main()
{
    int cse = 1;
    while(cin>>n)
    {
        init();
        if(n==0)
            return 0;
        for(int i=0; i<n; i++)
        {
            int u,v;
            cin>>u>>v;
            pic[u].push_back(v);
            pic[v].push_back(u);
        }
        for(int i=1; i<=n; i++)
            if(!pre[i])
                dfs(i, -1);
        ll ans1 = 0, ans2 = 1;
        for(int i=1; i<=bcc_cnt; i++)
        {
            ll cc=0;
            for(auto& j: bcc[i])
                if(iscut[j])
                    cc++;
            if(cc == 1)
            {
                ans1++;
                ans2 *= ((ll)bcc[i].size()-cc);
            }
        }
        if(bcc_cnt == 1)
        {
            ans1 = 2;
            ans2 = bcc[1].size()*(bcc[1].size()-1)/2;
        }
        cout<<"Case "<<cse<<": ";
        cout<<ans1<<" "<<ans2<<endl;
        cse++;
    }
    return 0;
}
```
