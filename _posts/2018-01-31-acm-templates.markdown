---
layout:     post
title:      "ACM-ICPC 算法模板"
subtitle:   "Algorithm Templates of ACM-ICPC"
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

* TOC
{:toc}

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

#### SPFA bfs判负环

```cpp
    struct node
    {
        int to, cost;
    };
    vector <node> G[MAXN];
    /*cnt: 每个点入队次数*/
    int d[MAXN], cnt[MAXN];
    bool vis[MAXN];
    int V;
bool spfa(int s)
{
    queue <int> q;
    fill(d, d + V + 1, INF);
    memset(cnt, 0, sizeof(cnt));
    memset(vis, 0, sizeof(vis));
    d[s] = 0;
    q.push(s);
    cnt[s]++;
    while (!q.empty())
    {
        int u = q.front();
        q.pop();
        vis[u] = false;
        for (auto& i: G[u])
        {
            int v = i.to;
            int c = i.cost;
            if (d[v] > d[u] + c)
            {
                d[v] = d[u] + c;
                if (!vis[v])
                {
                    q.push(v);
                    vis[v] = true;
                    cnt[v]++;
                    if (cnt[v] >= V + 1)
                        return false;
                }
            }
        }
    }
    return true;
}
```

### 生成树

#### Prim 邻接矩阵

```cpp
    int cost[MAXN][MAXN]; //结点i到j的最短距离，没有边初始化为INF
    int mincost[MAXN]; //从集合出发的边到每个点的最小权值
    bool used[MAXN]; //点是否在集合内
    bool tree[MAXN][MAXN]; //最小生成树，有向
    int pre[MAXN]; //最小生成树每个结点的父节点
    int V;
int prim(int s)
{
    fill(mincost, mincost + V + 1, INF);
    memset(used, 0, sizeof(used));
    memset(tree, 0, sizeof(tree));
    memset(pre, -1, sizeof(pre));
    mincost[s] = 0;
    int res = 0;
    while (true)
    {
        int v = -1;
        for (int u = 1; u <= V; u++) //结点从1开始
            if (!used[u] && (v == -1 || mincost[u] < mincost[v]))
                v = u;
        if (v == -1)
            break;
        used[v] = true;
        if (pre[v] != -1)
            tree[pre[v]][v] = true;
        res += mincost[v];
        int finu = INF;
        for (int u = 1; u <= V; u++) //结点从1开始
            if(mincost[u] > cost[v][u])
            {
                mincost[u] = cost[v][u];
                pre[u] = v;
            }
    }
    return res;
}
```

#### 次小生成树

删掉每条最小生成树的边后求解删边后的最小生成树。
```cpp
//需保证没有重边
//mintr: 最小生成树邻接矩阵表示
int ans = INF;
for(int i = 1; i <= V; i++) //结点从1开始
{
    for(int j = 1; j <= V; j++) //结点从1开始
    {
        if(mintr[i][j] == 1)
        {
            int c=cost[i][j];
            cost[i][j] = INF;
            cost[j][i] = INF;
            int temp=prim(1);
            if(temp >= res) //res: 最小生成树权值和
                ans = min(ans, temp);
            cost[i][j] = c;
            cost[j][i] = c;
        }
    }
}
```

### 网络流

```cpp
    struct edge
    {
        int to, rev;
        int cap;
    };
    vector <edge> G[MAXN];
    int level[MAXN];
    int iter[MAXN];
void add_edge(int from, int to, int cap)
{
    G[from].push_back({to, G[to].size(), cap});
    G[to].push_back({from, G[from].size() - 1, 0});
    return;
}

void bfs(int s)
{
    memset(level, -1, sizeof(level));
    queue <int> que;
    level[s] = 0;
    que.push(s);
    while (!que.empty())
    {
        int v = que.front();
        que.pop();
        for (auto& e: G[v])
            if (e.cap > 0 && level[e.to] < 0)
            {
                level[e.to] = level[v] + 1;
                que.push(e.to);
            }
    }
    return;
}

int dfs(int v, int t, int f)
{
    if (v == t)
        return f;
    for (int& i = iter[v]; i < G[v].size(); i++)
    {
        edge& e = G[v][i];
        if (e.cap > 0 && level[v] < level[e.to])
        {
            int d = dfs(e.to, t, min(f, e.cap));
            if (d > 0)
            {
                e.cap -= d;
                G[e.to][e.rev].cap += d;
                return d;
            }
        }
    }
    return 0;
}

int max_flow(int s, int t)
{
    int flow = 0;
    while (true)
    {
        bfs(s);
        if (level[t] < 0)
            return flow;
        memset(iter, 0, sizeof(iter));
        int f;
        while ((f = dfs(s, t, INF)) > 0)
            flow += f;
    }
}
```

### 二分图匹配

#### 匈牙利算法

```cpp
    int V;
    vector <int> G[MAXN];
    int match[MAXN];
    bool used[MAXN];
bool dfs(int v)
{
    used[v] = true;
    for (auto& u: G[v])
    {
        int w = match[u];
        if (w < 0 || !used[w] && dfs(w))
        {
            match[v] = u;
            match[u] = v;
            return true;
        }
    }
    return false;
}
int biparmat()
{
    int res = 0;
    memset(match, -1, sizeof(match));
    for (int v = 0; v < V; v++)
        if (match[v] < 0)
        {
            memset(used, 0, sizeof(used));
            if (dfs(v))
                res++;
        }
    return res;
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
    /*对于非连通图，需要每个点判断pre调用dfs*/
    /*iscut: 判断是否是割点*/
    /*bccno：每个点所在的BCC，割点的无意义*/
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

## 数据结构模板

### treap_set
```cpp
template <typename T>
class treap_set
{
public:
    treap_set(): root(NULL) {}
    void clear()
    {
        if (root!=NULL)
            clear(root);
    }
    void insert(T& x) {
        insert(root, x);
    }
    void erase(T& x) {
        remove(root, x);
    }
    bool find(T& x) {
        return find(root, x);
    }
    T kth(int k) {
        return kth(root, k);
    }
    int rank(T& x) {
        return rank(root, x);
    }
    int size() {
        return root->s;
    }
private:
    struct Node
    {
        Node* ch[2];//左右子树
        int r;//优先级，数值越大，优先级越高
        T v;//值
        int s;//结点总数
        int cmp(T x) const{
            if(x==v) return -1;
            return x<v?0:1;
        }
        void maintain()
        {
            s=1;
            if(ch[0]!=NULL) s+=ch[0]->s;
            if(ch[1]!=NULL) s+=ch[1]->s;
        }
    };
    Node* root;
    void clear(Node*& o)
    {
        if (o->ch[0]!=NULL)
            clear(o->ch[0]);
        if (o->ch[1]!=NULL)
            clear(o->ch[1]);
        delete o;
        o = NULL;
    }
    //d=0表示左旋，d=1表示右旋
    void rotate(Node* &o,int d)
    {
        Node* k=o->ch[d^1];
        o->ch[d^1]=k->ch[d];
        k->ch[d]=o;
        //注意先维护o，再维护k
        o->maintain();
        k->maintain();
        o=k;
    }
    //在以o为根的子树中插入键值x，修改o
    void insert(Node* &o,T& x)
    {
        if (find(root, x) == 1)
            return;
        if(o==NULL){
            o=new Node();
            o->ch[0]=o->ch[1]=NULL;
            o->v=x;
            o->r=rand();
        }
        else{
            int d=(x <= o->v ? 0 : 1);
            insert(o->ch[d],x);
            if(o->ch[d]->r>o->r){
                rotate(o,d^1);
            }
        }
        o->maintain();
    }
    void remove(Node* &o,T& x)
    {
        if (find(root, x) == 0)
            return;
        int d=o->cmp(x);
        if(d==-1){
            Node* u=o;
            if(o->ch[0]==NULL) {
                o=o->ch[1];
                delete u;
                u = NULL;
            }
            else if(o->ch[1]==NULL) {
                o=o->ch[0];
                delete u;
                u = NULL;
            }
            else{
                int d2=(o->ch[0]->r>o->ch[1]->r?1:0);
                rotate(o,d2);
                remove(o->ch[d2],x);

            }
        }
        else
            remove(o->ch[d],x);
        if(o!=NULL)
            o->maintain();
    }
    bool find(Node* o,T& x)
    {
        while(o!=NULL){
            int d=o->cmp(x);
            if(d==-1) return 1;//存在
            else o=o->ch[d];
        }
        return 0;//不存在
    }
    //找出第k小元素
    T kth(Node* o,int k)
    {
        if(o==NULL || k<=0 || k>o->s)
            return T();					//出错
        if(o->ch[0]==NULL && k==1)
            return (T)o->v;
        if(o->ch[0]==NULL)
            return kth(o->ch[1],k-1);
        if(o->ch[0]->s>=k)
            return kth(o->ch[0],k);
        if(o->ch[0]->s+1==k)
            return (T)o->v;
         return kth(o->ch[1],k-o->ch[0]->s-1);
    }
    //值x的“名次”，即比x小的结点个数加1
    int rank(Node* o,T& x)
    {
        if (find(root, x) == 0)
            return -1;
        if(o==NULL) return 1;
        int d=o->cmp(x);
        int tmp = 0;
        if (o->ch[0] != NULL)
            tmp = o->ch[0]->s;
        if(d==1) return tmp+1+rank(o->ch[1],x);
        return rank(o->ch[0],x);
    }

};
```

## 数学模板

### 二分快速乘

```cpp
ll qkmul(ll a ,ll b , ll m)
{
    ll sum=0;
    while(b)
    {
        if(b&1)
            sum=(sum+a)%m;
        a=(a+a)%m;
        b/=2;
    }
    return sum;
}
```
