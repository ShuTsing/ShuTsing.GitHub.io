---
layout:     post
title:      "CQUPT Training Nov28th - Dec3rd"
subtitle:   "Blog Passage Test"
date:       2016-11-28 15:28:00
author:     "ShuTsing"
header-img: "img/post-bg-4.jpg"
header-mask: 0.3
catalog:    true
tags:
    - BlogTest
    - 题解
    - 算法
---

从作业部落迁移过来进行测试。  

这个博客对markdown的支持有点迷。

## CQUPT Training Nov28th - Dec3rd
![banner.jpg-269kB][1]

---

## A - Design Tutorial: Learn from Math
### 题目大意
给出一个数n(12<=n<=10^6)， 求两个数x和y的其中一个值，要求x和y是合数且x+y=n。  
### 思路
筛法预处理小于n的素数，然后穷举寻找即可。
### 代码
{% highlight cpp linenos = table %}
#include <iostream>

#include <cstring>

using namespace std;
    bool vis[1000005];
int main(void)
{
    memset(vis,false,sizeof(vis));
    int n;
    cin>>n;
    int i,j;
    for(i=2;i<=1000000;i++)
        if(!vis[i])
            for(j=i*2;j<=1000000;j+=i)
                vis[j]=true;
    for(i=4;i<=1000000;i++)
    {
        if(!vis[i])
            continue;
        if(vis[n-i])
        {
            cout<<i<<" "<<n-i<<endl;
            break;
        }
    }
    return 0;
}
{% endhighlight %}
## B - Design Tutorial: Learn from Life
### 题目大意
有n个人，给出每个人期望到达的楼层和一个电梯的容量k(1 ≤ n, k ≤ 2000)，电梯每经过一层耗时一个单位，其余事项均不耗时，求电梯运完所有人的最小耗时。  
### 思路
电梯每一次运输的耗时只和将要到达最高楼层相关。贪心，用计数方法记录每个人到达的楼层数，然后从大到小选择即可。  
### 代码
~~~cpp
#include <iostream>  

using namespace std;
    int flag[2005]={0};
int main(void)
{
    int n,k;
    cin>>n>>k;
    int i;
    for(i=0;i<n;i++)
    {
        int a;
        cin>>a;
        flag[a]++;
    }
    int v=k,ans=0;
    for(i=2000;i>=2;i--)
    {
        while(flag[i])
        {
            if(v==k)
            {
                if(flag[i]>=v)
                {
                    flag[i]-=v;
                    v=0;
                }
                else
                {
                    v-=flag[i];
                    flag[i]=0;
                }
                ans+=(i-1)<<1;
            }
            else
            {
                if(flag[i]>=v)
                {
                    flag[i]-=v;
                    v=0;
                }
                else
                {
                    v-=flag[i];
                    flag[i]=0;
                }
                if(!v)
                    v=k;
                continue;
            }
            if(!v)
                v=k;
        }
    }
    cout<<ans<<endl;
    return 0;
}
~~~
## C - Design Tutorial: Make It Nondeterministic
### 题目大意
有n个人，给出每个人的姓和名，每个人用姓或者名参与一次字典序排序，给出一个排序顺序，问能否按照给出的顺序成功对这n个人进行字典序从小到大排序。  
### 思路
贪心，每个人都在比上一个人大的字符串中选择最小的一个。  
对于每个人，选取姓和名中在满足比给出顺序的上一个人选出的字符串的字典序大的情况下，字典序较小的一个。如果到某个人选不出来，则无法按给出顺序排序。  
### 代码
```cpp
#include <iostream>  

#include <string>  

using namespace std;
    string name[2][100005];
    int p[100005];
    bool flag[100005];
int main(void)
{
    int n;
    cin>>n;
    int i;
    for(i=1;i<=n;i++)
        cin>>name[0][i]>>name[1][i];
    for(i=1;i<=n;i++)
        cin>>p[i];
    if(name[0][p[1]]<name[1][p[1]])
        flag[p[1]]=0;
    else
        flag[p[1]]=1;
    for(i=1;i<=n-1;i++)
    {
        string si=name[flag[p[i]]][p[i]];

        bool ma;
        string sma;
        bool mi;
        string smi;
        if(name[0][p[i+1]]>=name[1][p[i+1]])
        {
            ma=0;
            sma=name[0][p[i+1]];
            mi=1;
            smi=name[1][p[i+1]];
        }
        else
        {
            mi=0;
            smi=name[0][p[i+1]];
            ma=1;
            sma=name[1][p[i+1]];
        }
        if(smi>si)
        {
            flag[p[i+1]]=mi;
        }
        else if(sma>si)
        {
            flag[p[i+1]]=ma;
        }
        else
        {
            cout<<"NO"<<endl;
            return 0;
        }
    }
    cout<<"YES"<<endl;
    return 0;
}
```
## E - MUH and Sticks  
### 题目大意  
给出6个数，如果4个相等，2个不等，则是Beer，如果4个相等，两个相等，则是Elephant，其他情况则是Alien。  
### 思路
判断6个数的相等情况即可。  
### 代码
```cpp
#include <iostream>  

#include <cstring>  

#include <algorithm>  

using namespace std;
    int arr[10]={0};
int main(void)
{
    int i;
    for(i=1;i<=6;i++)
    {
        int a;
        cin>>a;
        arr[a]++;
    }
    sort(arr,arr+10);
    if(arr[9]<4)
    {
        cout<<"Alien"<<endl;
        return 0;
    }
    arr[9]-=4;
    sort(arr,arr+10);
    if(arr[8]==0)
        cout<<"Elephant"<<endl;
    else
        cout<<"Bear"<<endl;
    return 0;
}
```
## F - MUH and Important Things
### 题目大意
有n个任务，给出每个任务的难度值（可能有相等的），要求在满足难度值从小到大排序的情况下，有至少3种不同的索引值排序方法，并输出其中3种排序。  
### 思路
易见，如果有至少3个任务的难度值相同，或者至少两个不同的2个任务的难度值相同，则可以满足有至少3种不同的排序方法，输出排序时，可以用STL algorithm 中的next_permutation函数求下一个排列。  
### 代码
```cpp
#include <iostream>  

#include <cstring>  

#include <vector>  

#include <algorithm>  

using namespace std;
    vector <int> arr[2005];
    vector <int> oput[3];
int main(void)
{
    int n;
    cin>>n;
    int i,j,k;
    for(i=1;i<=n;i++)
    {
        int a;
        cin>>a;
        arr[a].push_back(i);
    }
    int sum=0;
    for(i=1;i<=2000;i++)
    {
        if(arr[i].size()>1)
            sum+=arr[i].size();
    }
    if(sum<=2)
    {
        cout<<"NO"<<endl;
        return 0;
    }
    cout<<"YES"<<endl;
    int cnt=2;
    for(i=1;i<=2000;i++)
    {
        if(arr[i].size()==0)
            continue;
        if(arr[i].size()==1)
        {
            oput[0].push_back(arr[i][0]);
            oput[1].push_back(arr[i][0]);
            oput[2].push_back(arr[i][0]);
            continue;
        }
        for(k=0;k<arr[i].size();k++)
        {
            oput[0].push_back(arr[i][k]);
        }
        if(!(cnt%2))
            next_permutation(arr[i].begin(),arr[i].end());
        for(j=1;j<3;j++)
        {
            for(k=0;k<arr[i].size();k++)
            {
                oput[j].push_back(arr[i][k]);
            }
            next_permutation(arr[i].begin(),arr[i].end());
        }
        cnt++;
    }
    for(i=0;i<3;i++)
    {
        for(j=0;j<oput[i].size();j++)
        {
            cout<<oput[i][j];
            if(j!=oput[i].size()-1)
                cout<<" ";
        }
        cout<<endl;
    }
    return 0;
}
```
## G - MUH and House of Cards
### 题目大意  
给出棍子的个数n，两个棍子组成一个屋子，两个屋子之间要有一个棍子作为天花板（如图右），问在棍子全部利用完的情况下，可以有不同高度的数量。  
![图左并不符合要求，而图右符合][2]  
###  思路  
我们可以发现，每一层房子的棍子个数是2+3+3+...+3的数量，进一步分析，搭一层房子的棍子数k满足k mod 3 = 2 搭两层房子的棍子数满足 k mod 3 =1 搭三层满足 k mod 3 = 0 ……  
同时我们也知道，要得到最高的层数，每一层的房子要最少，那么可以先找出有剩余的情况下，能达到的最高层数m。那么，ans就是在m及以内，所有符合上面取模条件的层数，我们可得公式：  
    ans*3+(k mod 3)=m  
    answer=[ans]  
求解即可。  
### 代码
```cpp
#include <iostream>  

#include <algorithm>  

using namespace std;
int main(void)
{
    long long n;
    cin>>n;
    long long m=n%3;
    long long base=3;
    long long i;
    for(i=1;n>=0;i++)
    {
        n-=2;
        n-=base;
        base+=3;
    }
    i--;
    long long ans=(i+m)/3;
    cout<<ans<<endl;
    return 0;
}
```
## I - George and Accommodation
### 题目大意
给出n个房间，求有多少个房间的剩余空间>=2.  
### 思路
水题，判断即可。  
### 代码
```cpp
#include <iostream>

using namespace std;
int main(void)
{
    int n,ans=0;
    cin>>n;
    int i;
    for(i=0;i<n;i++)
    {
        int p,q;
        cin>>p>>q;
        if(q-p>=2)
            ans++;
    }
    cout<<ans<<endl;
    return 0;
}
```
## J - Fedor and New Game
### 题目大意
给出n种士兵和m+1个玩家，每个玩家有这n种士兵中的一些，用一个整数的二进制位表示是否拥有，如果前m个玩家和玩家m+1最多有k种不同，则是玩家m+1的朋友，问玩家m+1有多少朋友。  
### 思路
对两个玩家的值进行异或，异或结果为1的二进制位则是不同的，判断即可。  
### 代码
```cpp
#include <iostream>  

#include <cstring>  

using namespace std;

    int pler[1005];
int one(int x)
{
    int ans=0;
    while(x)
    {
        if(x%2)
            ans++;
        x>>=1;
    }
    return ans;
}
int main(void)
{
    int n,m,k,x_or,num,ans=0;
    cin>>n>>m>>k;
    int i,j;
    for(i=0;i<=m;i++)
        cin>>pler[i];
    for(i=0;i<m;i++)
    {
        x_or=pler[i]^pler[m];
        num=one(x_or);
        if(num<=k)
            ans++;
    }
    cout<<ans<<endl;
    return 0;
}
```
## K - George and Job
### 题目大意
有n个数，在这n个数中选出k组不相交的数，每组有m个数，找到能将这些选中的数的和最大的办法，输出这种方式下这些数的和。
### 思路
应该用dp来做，但是本弱的dp的姿势水平还不够，还是用了相较于dp更为熟悉的记忆化搜索的办法，居然没有超时，实在是感人啊。
run函数的两个参数n和k代表在前n个数和在前n个数选出k组，dp数组保存这种情况下和的最大值。
现给出状态转移方程：
    f(n,k)=max{f(n-1,k),f(n-m，k-1)+p[n]-p[n-m]};
m是每组的数的个数，p数组是n个数的前缀和。
### 代码
```cpp
#include <iostream>  

#include <cstring>  

#include <algorithm>  

using namespace std;

    long long p[5005];
    long long dp[5005][5005];
    int m;
long long run(int n,int k)
{
    if(dp[n][k]!=-1)
        return dp[n][k];
    if(n<1)
        return 0;
    if(k==0)
        return dp[n][k]=0;
    long long t=-1;
    if(n-m>=0)
        t=run(n-m,k-1)+p[n]-p[n-m];
    t=max(t,run(n-1,k));
    return dp[n][k]=t;
}
int main(void)
{
    int n,k;
    cin>>n>>m>>k;
    memset(dp,-1,sizeof(dp));
    int i;
    for(i=1;i<=n;i++)
    {
        cin>>p[i];
        p[i]+=p[i-1];
    }
    long long ans=run(n,k);
    cout<<ans<<endl;
    return 0;
}
```
## M - Cheap Travel
### 题目大意
某人要乘坐n次地铁，现有单次票和k次票若干，问怎么选择使其乘坐费用最小，输出最小费用。  
### 思路
题本身不难，但是有很多特殊情况，比如n<k，还有很多在生活中不可能的情况，比如k次票的性价比不如单次票，或者k次票的价格比单次票还便宜，所以WA了几发。  
### 代码

```cpp
#include <iostream>  

#include <algorithm>  

using namespace std;
int main(void)
{
    int n,m,a,b;
    int cost=0;
    cin>>n>>m>>a>>b;
    double bi=(double)b/(double)m;
    if(n>=m)
        {
        if(bi>(double)a)
            cost=n*a;
        else
        {
            cost+=(n/m)*b;
            cost+=min((n%m)*a,b);
        }
    }
    else
        cost=min(b,n*a);
    cout<<cost<<endl;
    return 0;
}
```

## N - Wonder Room  
### 题目大意  
### 思路  
### 代码  

```cpp
#include <iostream>  

#include <algorithm>  

#include <cmath>  

using namespace std;
int main(void)
{
    long long n,a,b;
    long long ans=100000000000000,ax,ay;
    cin>>n>>a>>b;
    n*=6;
    if(a*b>=n)
    {
        cout<<a*b<<endl;
        cout<<a<<" "<<b<<endl;
        return 0;
    }
    long long m=(long long)sqrt((double)n);
    long long i;
    for(i=a;i<=m;i++)
    {
        long long y=n/i;
        if(n%i)
            y++;
        y=max(y,b);
        if(i*y==n)
        {
            cout<<i*y<<endl;
            cout<<i<<" "<<y<<endl;
            return 0;
        }
        if(i*y<ans)
        {
            ans=i*y;
            ax=i;
            ay=y;
        }
    }
    for(i=b;i<=m;i++)
    {
        long long y=n/i;
        if(n%i)
            y++;
        y=max(y,a);
        if(i*y==n)
        {
            cout<<i*y<<endl;
            cout<<y<<" "<<i<<endl;
            return 0;
        }
        if(i*y<ans)
        {
            ans=i*y;
            ax=y;
            ay=i;//
        }
    }
    cout<<ans<<endl;
    cout<<ax<<" "<<ay<<endl;
    return 0;
}
```

## O - Number of Ways
### 题目大意
给出n个整数，要求将这些数分成3份，每份的数的和相等。
### 思路
首先，我们可以求出这堆数/3的值，如果不能被整除，那肯定是没招的，你不可能把他们分成小数，如果可以被整除，那么通过其前缀和，找到前缀和为sum/3和(sum/3)*2的即可。
### 代码
```cpp
#include <iostream>

#include <cstring>

using namespace std;
    long long fir[500005];
int main(void)
{
    int n;
    long long ans1=0,ans2=0;
    cin>>n;
    int i;
    for(i=1;i<=n;i++)
    {
        cin>>fir[i];
        fir[i]+=fir[i-1];
    }
    if(fir[n]%3)
    {
        cout<<"0"<<endl;
        return 0;
    }
    if(n<3)
    {
        cout<<"0"<<endl;
        return 0;
    }
    long long aver=fir[n]/3;
    for(i=1;i<n;i++)
    { //cout<<fir[i]<<endl;
        if(fir[i]==aver*2)
            ans2+=ans1;
        if(fir[i]==aver)
            ans1++;
    }
    cout<<ans2<<endl;
    return 0;
}
```


  [1]: http://static.zybuluo.com/PaulGuan/912k9zgcjb7xdtj289wx2qy3/banner.jpg  
  [2]: https://odzkskevi.qnssl.com/ac57500cb7525fc4f391b14297647b6b  
