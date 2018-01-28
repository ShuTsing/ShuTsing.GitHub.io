---
layout:     post
title:      "CQUPT 2018 寒假训练 解题报告 Div2 1.27 - 1.28"
subtitle:   "CQUPT Training Div2 Jan27th - Jan28th 2018"
date:       2018-01-27 20:16:00
author:     "ShuTsing"
header-img: "img/post-bg-7.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 数据结构
    - 算法
    - STL
    - 题解
---


# A - ACboy needs your help again!

**hdu - 1702**

## 题目大意

给出先进先出（队列）和先进后出（栈）两种结构， 每种均给出In和Out两类操作，如果是In，push进后面的数，如果是Out，输出栈顶（队首）。

## 解题思路

对于给的命令判断一下，然后用队列和栈求解即可。

## 代码

```cpp
#include <iostream>

#include <queue>

#include <stack>

#include <string>

using namespace std;
int main()
{
    int t;
    cin>>t;
    while (t--)
    {
        int n;
        string type;
        cin>> n>> type;
        if(type == "FIFO")
        {
            queue <int> q;
            while (n--)
            {
                string oper;
                cin>> oper;
                if (oper == "IN")
                {
                    int num;
                    cin>> num;
                    q.push(num);
                }
                else
                {
                    if (!q.empty())
                    {
                        cout<< q.front()<< endl;
                        q.pop();
                    }
                    else
                        cout<< "None"<< endl;
                }
            }
        }
        else
        {
            stack <int> s;
            while (n--)
            {
                string oper;
                cin>> oper;
                if (oper == "IN")
                {
                    int num;
                    cin>> num;
                    s.push(num);
                }
                else
                {
                    if (!s.empty())
                    {
                        cout<< s.top()<< endl;
                        s.pop();
                    }
                    else
                        cout<< "None"<< endl;
                }
            }
        }
    }
    return 0;
}

```

# B - Parentheses Balance

**uva - 673**

## 题目大意

给出一个字符串包含两种括号，()和[]，求判断是否符合标准。

## 解题思路

对于暂时没有后括号匹配的前括号压入栈，遇到后括号时看栈顶的前括号与其是否匹配，如果匹配则弹出该前括号。还要判断下最后栈里面是不是有剩下的没有匹配的前括号。

问题给出的字符串中包含空格，所以需要使用getline() 函数，用法如下：
```cpp
string s;
getline(cin, s);
```
另外非常玄学的是，在输入了最开始的组数（t）后，需要用一个getchar()函数把按下的回车字符给读了，不然接下来的getline()会多读入一行空白字符串。

## 代码
```cpp
#include <iostream>

#include <queue>

#include <stack>

#include <string>

using namespace std;
int main()
{
    int t;
    cin>> t;
    getchar(); //玄学处理法

    while (t--)
    {
        string str;
        getline(cin, str);
        stack <char> st;
        bool isyes = true;
        for (auto& i: str)
        {
            switch (i)
            {
                case '(':
                    st.push('(');
                    break;
                case ')':
                    if (!st.empty() && st.top() == '(')
                        st.pop();
                    else
                        isyes = false;
                    break;
                case '[':
                    st.push('[');
                    break;
                case ']':
                    if (!st.empty() && st.top() == '[')
                        st.pop();
                    else
                        isyes = false;
                    break;
            }
            if(isyes == false)
                break;
        }
        if(!st.empty())
            isyes = false;
        if(isyes == true)
            cout<< "Yes"<< endl;
        else
            cout<< "No"<< endl;
    }
    return 0;
}

```

# C - 简单计算器

**hdu - 1237**

## 题目大意

给一行四则运算计算式，求值。

## 解题思路

这道题从字符串处理到求解表达式都有不止一种做法，可以将字符串整行读入再进行拆分，而这里我给出了单个字符和数字读入的办法，会用到getchar()函数，因为getchar()在读入字符时会读入空格和回车，可以以此来判断是否读完一行，计算表达式可以用两个栈分开存储运算符和数字，我这里用的一个栈，如果是'+'的话，便将后面的那个数字压入栈中，'-'号的话，将后面的数字取反压入栈中，而如果遇到乘法或除法，则将栈顶取出与符号后面的数字计算后压入栈中，这样最后在栈中的数字全部相加即是表达式的值了。

## 代码

```cpp
#include <iostream>

#include <queue>

#include <stack>

#include <string>

#include <cstdio>

using namespace std;
    stack <double> st;
    char oper, xuanxue;
    double fir, num;
void init()
{
    if (!st.empty())
    {
        double ans = 0;
        while (!st.empty())
        {
            ans += st.top();
            st.pop();
        }
        printf("%.2lf\n", ans);
    }
    cin>> fir;
    st.push(fir);
    xuanxue = getchar();
    return;
}
int main()
{
    init();
    while(xuanxue == 10)
    {
        if (fir == 0)
            return 0;
        else
            init();
    }
    while (cin>> oper>> num)
    {
        xuanxue = getchar();
        switch (oper)
        {
            case '+': st.push(num); break;
            case '-': st.push(-num); break;
            case '*':
                num = st.top() * num; st.pop(); st.push(num); break;
            case '/':
                num = st.top() / num; st.pop(); st.push(num); break;
        }
        if (xuanxue == 10) //判断是不是换行

        {
            init();
            while (xuanxue == 10) //用while防止连续出现好几个都只有一个数字的表达式

            {
                if (fir == 0)
                    return 0;
                else
                    init();
            }
        }

    }
    return 0;
}
```

# D - Where is the Marble?

**uva - 10474**

## 题目大意

给出一个n个数的序列和q次查询，每次询问查询值是否在序列内，如果在，输出序列升序排列后的位置（从1开始）。

## 解题思路

先用sort()函数进行升序排列，然后用lower_bound() 函数进行二分查找，看找到的值是否为查询值，如果是，则符合条件。

## 代码
```cpp
#include <iostream>

#include <algorithm>

#include <string>

#define MAXN 10005

using namespace std;
    int num[MAXN];
int main()
{
    int n, q, cse = 1;
    while (cin>> n>> q)
    {
        if (n == 0 && q == 0)
            break;
        int i;
        for (i = 0; i < n; i++)
            cin>> num[i];
        sort(num, num + n);
        cout<< "CASE# "<< cse<< ":"<<endl;
        while (q--)
        {
            int val;
            cin>>val;
            int* idx = lower_bound(num, num + n, val);
            if (*idx == val)
                cout<< val<< " found at "<< idx - num + 1<< endl;
            else
                cout<< val<< " not found"<< endl;
        }
        cse++;
    }
    return 0;
}

```

# E - The Blocks Problem

**uva - 101**

## 题目大意

输入n，得到编号为0~n-1的木块，分别摆放在顺序排列编号为0~n-1的位置。现对这些木块进行操作，操作分为四种。  

1. move a onto b：把木块a、b上的木块放回各自的原位，再把a放到b上；

2. move a over b：把a上的木块放回各自的原位，再把a发到含b的堆上；

3. pile a onto b：把b上的木块放回各自的原位，再把a连同a上的木块移到b上；

4. pile a over b：把a连同a上木块移到含b的堆上。

当输入quit时，结束操作并输出0~n-1的位置上的木块情况。

## 解题思路

大模拟题，原先自己写了一个，写的很丑陋，后来看了网上一个人的题解，他将这四条提取出了3个操作并分别写成了三个函数供调用：

1. 找到某木块
2. 将某木块上的木块放回原位
3. 将某木块及其上面木块移动到另一个木块上

这样架构的代码结构更加清晰，编写不容器出错，我下面的代码便给出这个代码。

## 代码

```cpp
#include<iostream>

#include<algorithm>

#include<string>

#include<vector>

using namespace std;
const int maxn = 30;
int n;
vector<int> pile[maxn];
//找到木块a所在的pile和height，以引用的形式返回调用者

void find_block(int a, int& p, int& h)
{
	for (p = 0; p < n; p++)
        for (h = 0; h < pile[p].size(); h++)
            if (pile[p][h] == a)
                return;
    return;
}
//把第p堆高度为h的木块上方的所有木块移回原位

void clear_above(int p, int h)
{
	for (int i = h + 1; i < pile[p].size(); i++)
	{
		int m = pile[p][i];
		pile[m].push_back(m);
	}
	pile[p].resize(h + 1);
    return;
}
//把第p堆高度为h及其上方的木块整体移动到p2堆的顶部

void pile_onto(int p, int h, int p2)
{
	for (int i = h; i < pile[p].size(); i++)
		pile[p2].push_back(pile[p][i]);
	pile[p].resize(h);
	return;
}
void print()
{
	for (int i = 0; i < n; i++)
	{
		cout<< i<< ":";
		for (auto& j: pile[i])
			cout<< " "<< j;
		cout<< endl;
	}
	return;
}
int main()
{
	int a, b;
	cin>> n;
	string s1, s2;
	for (int i = 0; i < n; i++)
        pile[i].push_back(i);
	while (cin>> s1)
	{
	    if (s1[0] == 'q')
            break;
	    cin>> a>> s2>> b;
		int pa, pb, ha, hb;
		find_block(a, pa, ha);
		find_block(b, pb, hb);
		if (pa == pb) continue;
		if (s2 == "onto") clear_above(pb, hb);
		if (s1 == "move") clear_above(pa, ha);
		pile_onto(pa, ha, pb);
	}
	print();
	return 0;
}
```

# F - Andy's First Dictionary

**uva - 10815**

## 题目大意

给几段文章，拿出其中的英文单词，全部小写，并且按字典序排序。

## 解题思路

这里要说stringstream除了进行字符串和数字转换之外的另一个用途：分割单词。将字符串中所有非单词字符全部转换为空格，然后再用代码中的方法分割出单词。

C题简单计算器也可按照这种方法，将数字字符串和运算符字符串提取出来，然后再用一个stringstream将数字字符串转换为数字来做。

## 代码

```cpp
#include <set>

#include <string>

#include <iostream>

#include <sstream>

using namespace std;
set <string> st;
int main(void)
{
    string s, out;
    stringstream ssr;
    while(cin>>s)
    {
        for(char& i: s)
            if (i <= 65 + 26 - 1 && i >= 65)
                i += (97 - 65);
            else if (i < 97 || i > 97 + 26 - 1)
                i = ' ';
        ssr << s;
        while (ssr >> out)
            st.insert(out);
        ssr.clear();
    }
    for(auto& i: st)
        cout<< i<< endl;
    return 0;
}

```

# G - 水果

**hdu - 1263**

## 题目大意

中文题目，将每个产地的水果种类和数量按照字典序输出。

## 解题思路

因为string类自带字典序比较，所以这里产地和水果种类都用string类，用一个二维map容器，第一维以产地为键，水果为值，第二维以水果类型为键，水果数量为值，就可以自动按照字典序排序了。

## 代码

```cpp
#include <iostream>

#include <map>

#include <string>

using namespace std;
    map <string, map <string, int> > m;
int  main()
{
    int t;
    cin>> t;
    while (t--)
    {
        m.clear();
        int n;
        cin>> n;
        for (int i = 0; i < n; i++)
        {
            string kind, add;
            int num;
            cin>> kind>> add>> num;
            m[add][kind] += num;
        }
        for (auto& i: m)
        {
            cout<< i.first<< endl;
            for (auto& j: i.second)
                cout<< "   |----"<< j.first<< "("<< j.second<< ")"<< endl;
        }
        if (t)
            cout<< endl;
    }
    return 0;
}
```
