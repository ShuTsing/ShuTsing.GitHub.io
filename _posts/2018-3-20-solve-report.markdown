---
layout:     post
title:      "CQUPT 2018 春季训练 解题报告 3.12 - 3.19"
subtitle:   "CQUPT Training Mar12th - Mar19th 2018"
date:       2018-03-20 12:37:00
author:     "ShuTsing"
header-img: "img/post-bg-8.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - 题解
---

## E - Packets

**POJ - 1017**

### 题目大意

有若干6*6的箱子，有一些1*1 2*2 3*3 4*4 5*5 6*6的东西，问装完这些东西所需的最少的箱子要多少个。

### 解题思路

4*4 5*5 6*6 这三种尺寸的东西，一个就要占一个箱子，3*3的4个占一个箱子，然后剩下的1*1 2*2去补空，先2*2补，然后1*1补。

### 代码

```cpp
#include <iostream>
#include <cmath>
using namespace std;
int main()
{
    long long kazusa[7];
    while(true)
    {
        for(int haruki=1;haruki<=6;haruki++)
            cin>>kazusa[haruki];
        long long touma=0;
        for(int haruki=1;haruki<=6;haruki++)
            touma+=kazusa[haruki];
        if(!touma)
            break;
        long long setsuna=kazusa[4]+kazusa[5]+kazusa[6];
        setsuna+=ceil((double)kazusa[3]/4.0);
        long long yukinoshita=kazusa[4]* 5;
        long long yuigahama=kazusa[5]* 11;
        switch(kazusa[3]%4)
        {
            case 0: yukinoshita+=0; yuigahama+=0; break;
            case 1: yukinoshita+=5; yuigahama+=7; break;
            case 2: yukinoshita+=3; yuigahama+=6; break;
            case 3: yukinoshita+=1; yuigahama+=5; break;
        }
        if(yukinoshita>=kazusa[2])
            yuigahama+=(yukinoshita-kazusa[2])* 4;
        else
        {
            setsuna+=ceil((double)(kazusa[2]-yukinoshita)/9.0);
            if((kazusa[2]-yukinoshita)%9)
                yuigahama+=36-((kazusa[2]-yukinoshita)%9)* 4;
        }
        if(yuigahama<kazusa[1])
            setsuna+=ceil((double)(kazusa[1]-yuigahama)/36.0);
        cout<<setsuna<<endl;
    }
    return 0;
}

```

## G - Stripies

**POJ - 1862**

### 题目大意

n个物品，每个物品重量w_i，两个物品合并后重量变成2*sqrt(wa*wb)，问合并成一个物品时的最小重量。

### 解题思路

类似于哈夫曼树，就将合并方式改了就好，什么是哈夫曼树？快去复习你的数据结构吧。

### 代码

```cpp
#include <iostream>
#include <cmath>
#include <queue>
#include <cstdio>
#include <functional>
using namespace std;
    priority_queue <double,deque<double>,less <double> > megumi;
double eriri(){double sawamura;cin>>sawamura;return sawamura;}
int main()
{
    int asuna;
    cin>>asuna;
    for(int kirito=0;kirito<asuna;kirito++)
        megumi.push(eriri());
    double suguha=0;
    while(!megumi.empty())
    {
        double kasumigaoka=megumi.top();
        megumi.pop();
        if(megumi.empty())
        {
            suguha=kasumigaoka;
            break;
        }
        double utaha=megumi.top();
        megumi.pop();
        double shino=2.0*sqrt(kasumigaoka*utaha);
        megumi.push(shino);
    }
    printf("%.3f\n",suguha);
    return 0;
}
```
## H - Protecting the Flowers

**POJ - 1862**

### 题目大意

n头牛，每头牛的吃草时间为ti，返回时间为di，现在开始牵牛回去，求最少被吃掉的草的量。

### 解题思路

贪心算法，不难得出贪心策略为先牵t/d最小的牛。

### 代码

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
    struct mahoushoujo
    {
        int madoka;
        int homura;
    };
    mahoushoujo qb[100005];
    int tsubasa[100005];
bool enkannokotowari(mahoushoujo sayaka,mahoushoujo mami)
{
    if((double)sayaka.madoka/(double)sayaka.homura<(double)mami.madoka/(double)mami.homura)
        return true;
    return false;
}
int  main()
{
    int senjougahara;
    cin>>senjougahara;
    for(int araragi=0;araragi<senjougahara;araragi++)
        cin>>qb[araragi].madoka>>qb[araragi].homura;
    sort(qb,qb+senjougahara,enkannokotowari);
    for(int araragi=senjougahara-2;araragi>=0;araragi--)
        tsubasa[araragi]=tsubasa[araragi+1]+qb[araragi+1].homura;
    long long hachikuji=0;
    for(int araragi=0;araragi<senjougahara;araragi++)
        hachikuji+=2*qb[araragi].madoka*tsubasa[araragi];
    cout<<hachikuji<<endl;
    return 0;
}
```

## L - Children's Game

**UVA - 10905**

### 题目大意

给出n个数，求合并起来最大的数。

### 解题思路

对这n个数进行排序，不过需要注意的是，排序的方式并不是从大向小排，而是两个数a和b，取字符串形式的a+b和b+a谁大来进行比较。

### 代码

```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;
int main()
{
    string s[55];
    int n;
    while(cin>>n)
    {
        if(!n)
            break;
        for(int i=0;i<n;i++)
            cin>>s[i];
        sort(s,s+n,[](string a,string b){return a+b>b+a;});
        for(int i=0;i<n;i++)
            cout<<s[i];
        cout<<endl;
    }
    return 0;
}
```


## M - Entropy

**ZOJ - 1117**

### 题目大意

给出一段文字，求哈夫曼编码和8位编码的长度和长度比值。

### 解题思路

板子哈夫曼，哈夫曼编码的长度就是各个非叶子结点的权值之和。

### 代码

```cpp
#include <iostream>
#include <string>
#include <cstring>
#include <cstdio>
#include <queue>
using namespace std;
    priority_queue <int,deque<int>,greater<int> > pq;
    int cnt[256];
int main(void)
{
    string s;
    while(cin>>s)
    {
        if(s=="END")
            break;
        memset(cnt,0,sizeof(cnt));
        for(auto i: s)
            cnt[i]++;
        for(auto i: cnt)
            if(i)
                pq.push(i);
        int ans=0;
        while(pq.size()>1)
        {
            int a=pq.top();
            pq.pop();
            int b=pq.top();
            pq.pop();
            ans+=a+b;
            pq.push(a+b);
        }
        if(!ans)
            ans=s.size();
        cout<<s.size()* 8<<" "<<ans<<" ";
        printf("%.1f\n",(double)s.size()* 8.0/(double)ans);
        pq.pop();
    }
}

```

## N - Tian Ji -- The Horse Racing

**ZOJ - 2397**

### 题目大意

田忌赛马，问田忌最多能拿到多少赏金。

### 解题思路

如果田忌当前最慢的马比齐王当前最慢的慢，就拿田忌当前最慢的去和齐王当前最快的比；    
如果田忌当前最慢的马比齐王当前最慢的快，就拿田忌当前最慢的去和齐王当前最慢的比；     
如果田忌当前最慢的马和齐王当前最慢的一样快，如果当前田忌最快的比齐王最快的快，就先留着最慢的，把最快的比了，否则就拿最慢的去和齐王最快的比。

### 代码

```cpp
#include <iostream>
#include <list>
#include <algorithm>
using namespace std;
    list <int> k,t;
int main()
{
    int n;
    while(cin>>n)
    {
        if(!n)
            break;
        k.clear();
        t.clear();
        auto input=[](){int num;cin>>num;return num;};
        for(int i=0;i<n;i++)
            t.push_back(input());
        for(int i=0;i<n;i++)
            k.push_back(input());
        t.sort(greater <int> ());
        k.sort(greater <int> ());
        int ans=0,orz=0;
        while(!k.empty())
        {
            if(t.back()<k.back())
            {
                t.pop_back();
                k.pop_front();
                orz++;
            }
            else if(t.back()>k.back())
            {
                t.pop_back();
                k.pop_back();
                ans++;
            }
            else
                if(t.front()>k.front())
                {
                    t.pop_front();
                    k.pop_front();
                    ans++;
                }
                else
                {
                    if(t.back()!=k.front())
                        orz++;
                    t.pop_back();
                    k.pop_front();
                }
        }
        cout<<(ans-orz)* 200<<endl;
    }
    return 0;
}

```

### O - Regular Bracket Sequence

**CodeForces - 26B**

### 题目大意

给一串括号，问最大匹配括号数。

### 解题思路

栈的模板题，事实上栈都用不上就可以做。

### 代码

```cpp
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string s;
    cin>>s;
    int cnt=0;
    int sum=0;
    for(auto i: s)
        if(i=='(')
           sum++;
        else
            if(sum==0)
                cnt++;
            else
                sum--;
    cout<<s.size()-cnt-sum<<endl;
}
```
```

### P - A Student's Dream

**CodeForces - 62A**

### 题目大意

男孩和女孩牵手，女孩两个手指之前要有至少一个男孩手指，男孩的三个手指不能并在一起。

### 解题思路

设男孩手指个数b，女孩手指个数g不难得出b的范围是[g-1, (g+1)*2]。

### 代码

```cpp
#include <iostream>
#include <cmath>
using namespace std;
int main()
{
    int a,b,x,y;
    cin>>a>>b>>x>>y;
    if(x>=b-1&&x<=2*b+2||y>=a-1&&y<=2*a+2)
        cout<<"YES"<<endl;
    else
        cout<<"NO"<<endl;
    return 0;
}

```
