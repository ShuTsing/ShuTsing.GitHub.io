---
layout:     post
title:      "C++ STL在ACM中的应用"
subtitle:   "Use C++ STL in ACM-ICPC"
date:       2018-01-13 13:12:00
author:     "ShuTsing"
header-img: "img/post-bg-17.jpg"
header-mask: 0.3
catalog:    true
tags:
    - STL
    - 算法
    - C++
---

## 写在前面  
### 什么是STL？  
STL是 Standard Template Library 的简称，中文名为标准模板库，是惠普实验室开发的一系列软件的统称。（从百度百科抄的  
MSVC，clang和gcc等编译器厂商都会根据C++标准委员会制定的STL标准自行编写STL，但是基本都是基于最早惠普实验室开发的这个版本。  
### 为什么要在竞赛中使用STL？  
STL给我们提供了很多算法竞赛中经常使用到的算法和数据结构，如：

- 提供的算法：快速排序，全排列，二分搜索……  
- 提供的数据结构：动态数组，二叉排序树，优先队列……  

灵活使用这些STL中的算法和数据结构可以有效避免在比赛中重复造轮子，加快代码的编写速度，同时避免出现Bug。

基于以上原因，算法选手需要能够较好的将STL作为黑盒使用，并了解其提供的操作以及操作的时间复杂度。  

但是我在接下来还是会稍微讲一下STL的原理以期能更好的应用。  
### 为STL运行效率申冤  
很多高中打过某O字开头的竞赛的同学（包括我）都对STL的运行效率有一种固有的低下的印象。  
我个人认为可能有下列三点原因：  

- 某O字开头的竞赛在编译时不开编译器优化
- 对语言为了实现多态而影响运行效率的印象
- 某些选手在使用中没有选用合适的容器和方法（函数）

事实上，对于一个发行版本的程序来说，基本上是一定会开编译器优化的，如果不开编译器优化则一般是在调试阶段。ACM-ICPC还是给了选手发布Release版本的机会的（笑）即开-O2优化。  
而STL出于工程上的考虑，在debug状态下会进行诸多安全性检查和验证。    

一般来说，通过虚函数和继承来实现的运行期多态确实会影响运行效率，某J字开头的语言所提供的泛型本质上是个语法糖，还是实现的运行期多态。  
然而C++用以实现泛型编程的Template（模板）则是编译器多态，在编译时就会确定好类型，不会影响运行效率。（所以C++是世界上编译速度最慢的语言（逃  

STL的很多方法的方法（成员函数）名称的命名会考虑到时间复杂度，比如各个容器的push_back()，pop_back()，push_front()等方法的时间复杂度都是O(1)的，这也是为什么容器vector不提供push_front()函数的原因，vector在头部插入的时间复杂度是O(n)的。所以在能用这类方法的时候就尽量不用insert()和erase()方法。  


## 前置技能  
虽然说是前置技能，但是其实不需要怎么掌握也能用STL（逃  
但是我认为如果能够比较了解这些内容的话，可以更好的使用STL（但是只会一带而过的讲，如果之前语言基础不牢固还要去查其他资料）。

### 指针
指针在算法竞赛中能够使用到的地方真是少之又少，除了trie（字典树）等几个算法或数据结构外几乎都可以用其他方法替代（包括trie也是可以用数组模拟的）。  
除此之外的地方，指针也基本上可以被引用替代。  
不过还是举一个例子来看看指针在传参的用途，有兴趣的可以再去了解。   
```cpp
void swap(int* x, int* y)
{
  int temp = * x;
  * x = * y;
  * y = temp;
}
int main(void)
{
  int a = 1, b = 2;
  swap(&a, &b);
  cout<< a<< " "<< b<< endl;
  return 0;
}
```

输出结果： 2  1  

### 引用
其实引用能用到的地方也是很少了，在算法竞赛中。不过还是有，比如白书上扩展欧几里得算法的实现就用到了引用传参。那我们再来看看用引用怎么传参：  

```cpp
void swap(int& x, int& y)
{
  int temp = x;
  x = y;
  y = temp;
}
int main(void)
{
  int a = 1, b = 2;
  swap(a, b);
  cout<< a<< " "<< b<< endl;
  return 0;
}
```

输出结果： 2  1  

和指针传参效果一样，但是相比于用指针传递参数而言要方便很多。在要传占用内存较大的变量或类时可以用指针传递来加快效率。

### 运算符重载
你要是能在算法竞赛中遇到需要用运算符重载的情况的话，那真是撞大运了（其实就是也用的很少（逃。但是同前两个一样，在一些场合还是需要的。

- 高精度计算，这不是这篇要讲的东西（用Java去）
- 进行结构体或类的比较运算（比如你突发奇想打算在set里面装结构体）

运算符重载简而言之就是重新赋予C++原有的运算符新的意义。因为C++自带的类型有限，有时你需要去实现自定义的类或者结构体之间的运算。

让我们来看下面代码，我们需要将node以x为第一关键字，y为第二关键字按升序排序：  
```cpp
  struct node
  {
    int x;
    int y;
    bool operator < (const node& a) const
    {
      if (x < a.x)
        return true;
      else if (x == a.x && y < a.y)
        return true;
      return false;
    }
  };
  set <node> st;
int main(void)
{
  st.insert((node){2, 4}); //注意，这种写法是C++ 11添加的   

  st.insert((node){1, 3});
  st.insert((node){2, 2});
  st.insert((node){1, 2});
  for (auto i: st)
    cout<< i.x<< " "<< i.y<< endl;
  return 0;
}
```

输出结果：  
1 1  
1 3  
2 2  
2 4  

除了这种方法外，大多数运算符还能重载到结构体或类的外部。  
比如，上面的运算符重载函数还能在node外写成：  
```cpp
bool operator < (const node& a, const node& b)
{
  if (a.x < b.x)
    return true;
  else if (a.x == b.x && a.y < b.y)
    return true;
  return false;
}
```

### auto关键字
auto 关键字在上古时代的C++便已经存在，但是因为其含义实在太废了（用于声明变量为自动变量，不声明也是）所以几乎没人会用到这个关键字。  
而在C++ 11标准中加入的类型推断重新定义了auto关键字的含义，现在不少ACM-ICPC，CCPC 的赛区还有一些oj都加入对C++ 11标准的支持，所以我们可以在比赛中使用这些C++ 11标准的特性。

auto关键字现在可以在声明变量的时候根据变量初始值的类型自动为此变量选择匹配的类型，配合同是C++ 11标准加入的for语句的新用法在有的时候可以加快代码的编写速度。

比如在之前的一个地方，出现了下面这种写法：  
```cpp
for (auto i: st)
  cout<<i<<endl;
```  
这种写法中，for循环会从头到尾遍历一遍st容器，将其中的值取出。需要注意的是，如果要修改元素的值的话，应该声明成引用auto& i。

如果这里用原来的写法则要写成：
```cpp
set <int> iterator::itr;
for (itr = st.begin(); itr != st.end(); itr++)
  cout<<*itr<<endl;
```
这种写法在数组上效果不好，毕竟很少有人会遍历完整个数组，但是在C++ STL所提供的容器上面使用这种for语句的用法效果就很不错。  

需要注意的是，auto关键字是类型推断而不是动态类型，用auto声明的变量必须初始化，并且确定类型后就不能更改。  

### 模板
STL的中文名称就叫标准模板库，那么什么是模板呢？  
模板是C++支持参数化多态的工具。（gun...）  
上面那句话不是给人听的，我们发现，我们在之前的演示代码中使用到的STL的容器时，会用到这么个玩意：  
```cpp
<Typename>
```
用这玩意来指定容器中元素的类型，这就是类模板的用法了。  
那么除了类模板，还有函数模板。比如STL的算法库（algorithm）里面提供的swap()函数就用到了函数模板，所以swap里面可以交换各种各样的变量，包括你自定义的类或结构体。  
不过因为算法竞赛中只会去使用模板造的轮子而不会去用模板造轮子，在这里就不去进一步阐述C++的模板的用法了。
## 正片开始
终于介绍完了前面那一堆语言特性了……  
现在就来详细介绍STL里面的各个容器和算法的使用方法了。  
在算法竞赛中，我们会使用到STL的以下几个部分：  
- 容器（再算上字符串类）
- 算法
- 迭代器

其中迭代器的讲解将会放在容器的讲解里面。下面就开始吧。
### 容器
容器是管理序列的类，通过容器提供的成员函数，实现各种对序列中元素的操作。  
简而言之，就是，C++ STL的容器能够提供除了数组外的其他的数据结构，像什么栈（stack），队列（queue），二叉排序树（BST）什么的，来供我们使用。  

容器分为以下三类：  
- 序列式容器：元素有序但未排序  
比如像什么vector，list之类的，你元素存进去是什么顺序，在容器里面的排列顺序就是什么样子的。  

- 关联式容器：元素均经过排序  
比如set，map之类的，在容器里面的排列顺序是通过某一种定义的排序方式来进行排序的，比如你存进去的时候是 5 2 1 3 这么来存的，在容器里面则是按照 1 2 3 5 这么来排的。  

- 容器配接器：以某容器为底修改接口  
比如queue，stack这种，是通过进一步封装其他容器得到的。

下面会逐一介绍STL各个容器的用法。

#### vector
说实话我不是很懂为什么C++ STL要把动态数组取名为vector而不是ArrayList，这直接导致我写向量类的时候取名异常纠结。   

使用vector容器需要声明头文件：
```cpp
#include <vector>
```
vector的元素可以是任意类型T，但必须具备赋值和拷贝能力。

**- vector对象的定义和初始化**  

STL 容器的很多参数和返回值，往往并不是int，而是size_t，这个类型在32位机器为unsigned in
t，64位机器上为unsigned long，下面给出的参数类型统一都用int代替。

```cpp
vector <T> vec; // vector保存类型为T的对象。默认构造函数vec为空。

vector <T> vec(vector <T> vec2); // vec是vec2的一个副本

vector <T> vec(int n, T i); // vec含有n个数值为i的元素

vector <T> vec(int n); // vec含有n个数值为0的元素

vector <T> vec(itr beg, itr end); // 产生一个vector，以区间[beg,end]为元素初值

```
**- vector的大小和容量**  

vector类定义了方法（成员函数）size()和capacity()来实现统计容器元素的目的。其中，size()返回容器中现有的元素数量（以下简称数量），capacity()返回容器目前能容纳的元素数量（以下简称容量）。

size()函数好理解，就是返回vector里面存了多少个元素，但是capacity()函数的返回值是什么意义呢？这就需要了解一下STL vector的实现原理了。

vector在开辟内存空间时是开辟的一段定长的连续空间，假设长度为n，当插入的元素的数量超过了n时，就会去新开辟一片比n更长的连续空间，把原来空间里面的值给拷贝到新空间，然后释放原来空间的内存。而capacity()函数则是返回的当前的n的大小。

从上面的描述不难想象，如果插入的元素个数大于n后，重新申请内存和拷贝数据是比较浪费时间的，所以如果有能够确定下你将要在这个vector里面存放的元素数量时，最好在初始化的时候就声明出来。（当然算法竞赛中一般都没办法确定）

如果需要重新设置数量用resize()方法，如果需要重新设置容量用reserve()方法。

另外vector还提供了一个方法max_size()返回vector能存入的最大元素个数，这个值非常的大，一般在算法竞赛中都不需要考虑。

**- vector中元素的访问**  

因为运算符重载的存在，vector可以直接通过下标运算符[]来进行随机访问，vector也提供了方法at()来进行随机访问，相比于通过[]来访问，at()方法会在运行时检查下标是否越界，但是越界检查会影响运行效率，在调试中可以使用at()方法，但是在最后提交中一般建议使用[]来进行随机访问。

此外，vector容器还提供了front()和back()方法用于读取或修改头尾的元素。  

```cpp
T& v[int idx];

T& v.at(int idx);

T& v.front();

T& v.back();
```

具体用法，便是：  
```cpp
vector <int> v(5);

int a = v[0];
a = v.at(0);
a = v.front();
a = v.back();

v[0] = a;
v.at(0) = a;
v.front() = a;
v.back() = a;
```

以上的访问方法的时间复杂度均为O(1)。

**- vector中元素的遍历**  

终于要将迭代器了。
vector容器中的元素有两种遍历方法，一种是通过下标像数组一样遍历：  
```cpp
vector <int> v;
for (int i = 0; i < v.size(); i++)
  cout<<v[i]<<endl;
```  
还有一种方法，就是通过迭代器进行遍历。  

迭代器简单的来说就是将容器内部的指针进行了封装。

我们想一想普通数组如果用指针遍历应该怎么办呢？
```cpp
int n = 100;
int arr[n + 1];
for(int* p = arr; p != &arr[n]; p++)
  cout<< *p<< endl;
```

而迭代器的声明如下：
```cpp
vector <T> iterator::itr;
```
这个迭代器就相当于这个容器用于遍历的指针，用法如下：
```cpp
for(itr = v.begin(); itr != v.end(); itr++)
  cout<< *itr<< endl;
```
begin()和end()是vector容器提供的两个方法，前者返回头部元素的位置，后者返回的是尾部元素之后的位置。

而我们如果需要对元素进行逆向的遍历，则需要更换另一种迭代器，同时声明头文件itreator。
```cpp
#include <iterator>

vector <int> v;
vector <int> reverse_iterator::ritr;

for(ritr = v.rbegin(); ritr != v.rend(); ritr++)
  cout<< *ritr<< endl;
```
rbegin()方法返回头部元素之前的位置，rend()返回尾部元素的位置。

对于不需要用到下标的正向遍历，我们可以用下面这种C++ 11标准加入的for语句使用方法：
```cpp
for(int i: v)
  cout<< i<< endl;
for(int& i: v)
  i = 1;
```
注意的是，如果需要修改容器的元素，一定要将i声明成引用。

而其他需要用到迭代器的地方，我们也可以用auto关键字进行简化：
```cpp
for(auto i = v.rbegin(); i != v.rend(); i++)
  cout<< *i<< endl;
```

两个同类迭代器之间可以用成员函数distance()来求距离：
```cpp
#include <iterator>

distance(first, last);//返回last - first的距离  
```
需要注意的是，distance()方法只有在诸如vector，deque等能随机访问元素的容器的迭代器上面有较好的效率，即时间复杂度为O(1)，反之，在诸如list，set等不支持随机访问元素的容器的迭代器上面则需要O(n)的时间复杂度来完成距离计算。  

两个同类型的迭代器还能用iter_swap()方法进行交换
```cpp
#include <iterator>

iter_swap(itr1, itr2);
```


**- vector中元素的插入**

vector容器提供了push_back()方法用于在尾部插入元素，同时提供了insert()方法进行随机插入。

push_back()的时间复杂度为O(1)。   
insert()的时间复杂度为O(n)。

方法如下：
```cpp
void v.push_back(T e); //在尾部插入e的副本

itr v.insert(itr pos, T e); //在pos位置插入元素e的副本，并返回新元素位置

itr v.insert(itr pos, int n, T e); //在pos位置插入n个元素e的副本

itr v.insert(itr pos, itr beg, itr end); //在pos位置插入区间[beg,end]内所有元素的副本

```
具体用法如下：
```cpp
std::vector<int> v(4, 2);
v.insert(v.begin() + 1, 3);
```
在插入之前，v中的元素为：  

Index | Value
:-: | :-:
 0 | 2
 1 | 2
 2 | 2
 3 | 2

 插入之后，变为了：

 Index | Value
 :-: | :-:
  0 | 2
  1 | 3
  2 | 2
  3 | 2
  4 | 2

**- vector中元素的删除**  

vector容器提供了pop_back()方法用于在尾部删除元素，同时提供了erase()方法进行随机删除。

pop_back()的时间复杂度为O(1)。   
erase()的时间复杂度为O(n)。

方法如下：
```cpp
void v.pop_back(); //移除尾部元素但不返回

itr v.erase(itr pos); //删除pos位置的元素，返回下一个元素的位置

itr v.erase(itr beg, itr end); //删除区间[beg,end]内所有元素，返回下一个元素的位置

```

**- vector中的操作符**

如果vector里面存放元素是简单类型（C++自带的那些类型）的话，可以使用操作符==和!=判断两个vector是否相等。还可以通过>, <, >=, <=操作符比较两个vector容器内元素的字典序。

**- vector中其他方法**

1. 清空元素 方法clear()，时间复杂度O(n)。  
注意clear()方法只清空元素并不释放内存。

2. 判断为空 方法empty()返回值为容器的数量是否为空。

3. 交换元素 方法swap()  
使用方法：v1.swap(v2);

4. 拷贝元素 方法assign()  
使用方法：v.assign(int n, T e); 将n个元素e赋值给v  
v.assign(itr begin, itr end); 将区间[begin, end]的元素赋值给v

#### list


list是C++ STL提供的双向链表容器。不支持随机存取，但是有良好的插入和删除效率。

使用list容器需要声明头文件：  
```cpp
#include <list>
```
**- list对象的定义和初始化**  

```cpp
list <T> l; // 产生空的list

list <T> l(list <T> l2); // 生成一个复制了l2的所有元素的l

list <T> l(int n); // 生成一个含有n个空元素的的list

list <T> l(int n, T e); // 生成一个含有n个值为e的元素的list

list <T> l(itr beg, itr end); //产生一个list，以区间[beg,end]为元素初值
```

**- list的大小和容量**

list容器基于双向链表，不会出现如vector容器那样的元素数量和元素容量不同的问题。  

list容器提供了size()方法返回当前元素个数，并提供了max_size()方法返回所能容纳的最大元素个数。    
需要注意的是，对于gcc编译器，只有在gcc 5.0及以上并开启了C++ 11及以上标准的编译指令后，size()方法的时间复杂度为O(1)，在其他情况下，gcc所实现的list.size()的时间复杂度为O(n)。

list容器的max_size同样非常大，在算法竞赛中一般不需要关心。  

**- list中元素的访问**

list容器不支持随机访问，仅提供front()和back()方法访问首尾元素。
```cpp
T& l.front();

T& l.back();
```

**- list中元素的遍历**

list容器不能通过下标进行元素遍历，只能通过迭代器进行遍历:  

```cpp
list <T> iterator::itr;
for (itr = lt.begin(); itr != lt.end(); itr++)
  cout<< *itr<< endl;

for (T i: lt)
  cout<< i<< endl;
```

**- list中元素的插入**


```cpp
void l.push_front(T e); //在头部插入e的副本

void l.push_back(T e); //在尾部插入e的副本

itr l.insert(itr pos, T e); //在pos位置插入元素e的副本，并返回新元素位置

itr l.insert(itr pos, int n, T e); //在pos位置插入n个元素e的副本

itr l.insert(itr pos, itr beg, itr end); //在pos位置插入区间[beg,end]内所有元素的副本

```
**- list中元素的删除**   


list容器提供了用于进行选择删除的方法。

```cpp
void l.pop_front(); //移除头部元素但不返回

void l.pop_back(); //移除尾部元素但不返回

itr l.erase(int pos); //删除pos位置的元素，返回下一个元素的位置

itr l.erase(itr beg, itr end); //删除区间[beg,end]内所有元素，返回下一个元素的位置

void l.remove(T e); //删除所有值为e的元素

void l.remove_if(func f); //删除所有func(e) == true 的元素

void l.unique(); //删除所有重复元素

void l.unique(func f); //删除所有func(e)  =true 的重复元素
```

**- list中的操作符**

如果list里面存放元素是简单类型的话，可以使用操作符==和!=判断两个list是否相等。还可以通过>, <, >=, <=操作符比较两个list容器内元素的字典序。

**- list中元素的排序**

```cpp
void l.sort(); //按照元素的<操作符进行排序

void l.sort(func f); //按照f函数为准则对元素进行排序
```

**- list中元素的拷贝**

```cpp
void l.assign(int n, T e); //将n个元素e赋值给l

void l.assign(itr begin, itr end); //将区间[begin, end]的元素赋值给l

void l.merge(list l2); //若l, l2都已排序，那么将l2元素拷贝到l并保证拷贝后的序列有序

void l.splice(itr pos, list l2); //将l2的元素拷贝到l的pos位置之前

void l.splice(itr pos, list l2, itr l2pos); //将l2pos所指元素拷贝到pos之前

void l.splice(ire pos, list l2, itr beg, itr end); //将l2的[beg, end]内元素拷贝到pos之前
```

**- list中的其他方法**

1. 清空元素 方法clear()，时间复杂度O(n)。  

2. 判断为空 方法empty()返回值为容器的数量是否为空。

3. 交换元素 方法swap()  
使用方法：l1.swap(l2);

4. 反转元素顺序 方法reverse()。


#### deque

deque是C++ STL提供的双端队列容器，支持头尾的增删操作。

因为deque的内部实现与vector不同（deque会用多段定长连续空间实现），deque容器的随机存取速度会慢于vector容器。

要使用deque容器，应包含头文件
```cpp
#include <deque>
```

**- deque对象的定义和初始化**  

```cpp
deque <T> d; // 产生空的deque

deque <T> d(deque <T> d2); // 生成一个复制了d2的所有元素的d

deque <T> d(int n); // 生成一个含有n个空元素的的deque

deque <T> d(int n, T e); // 生成一个含有n个值为e的元素的deque

deque <T> d(itr beg, itr end); //产生一个deque，以区间[beg,end]为元素初值
```

**- deque的大小和容量**

deque容器提供了size()方法返回当前元素个数，并提供了max_size()方法返回所能容纳的最大元素个数。

deque容器的max_size同样非常大，在算法竞赛中一般不需要关心。

**- deque中元素的访问**

deque容器虽然支持随机访问，但是其随机访问速度慢于vector容器。

```cpp

T& d[int idx];

T& d.at(int idx);

T& d.front();

T& d.back();
```

**- deque中元素的遍历**

deque容器支持随机存取，所以可以像数组那样通过下标进行遍历，但是因为deque的随机访问较慢，所以并不推荐这种写法：
```cpp
for (int i = 0; i < d.size(); i++)
  cout<< d[i]<< endl;
```

deque容器支持迭代器遍历：

```cpp
dequen <T> iterator::itr;
for (itr = d.begin(); itr != d.end(); itr++)
  cout<< *itr<< endl;
```

**- deque中元素的插入**

```cpp

void d.push_front(T e); //在头部插入e的副本

void d.push_back(T e); //在尾部插入e的副本

itr d.insert(itr pos, T e); //在pos位置插入元素e的副本，并返回新元素位置

itr d.insert(itr pos, int n, T e); //在pos位置插入n个元素e的副本

itr d.insert(itr pos, itr beg, itr end); //在pos位置插入区间[beg,end]内所有元素的副本

```

**- deque中元素的删除**

```cpp

void d.pop_front(); //移除头部元素但不返回

void d.pop_back(); //移除尾部元素但不返回

itr d.erase(int pos); //删除pos位置的元素，返回下一个元素的位置

itr d.erase(itr beg, itr end); //删除区间[beg,end]内所有元素，返回下一个元素的位置

void d.clear(); //清空元素
```

**- deque中的操作符**

如果deque里面存放元素是简单类型的话，可以使用操作符==和!=判断两个deque是否相等。还可以通过>, <, >=, <=操作符比较两个deque容器内元素的字典序。

**- deque中元素的拷贝**

```cpp
void l.assign(int n, T e); //将n个元素e赋值给d

void l.assign(itr begin, itr end); //将区间[begin, end]的元素赋值给d
```

**- deque中的其他方法**

1. 清空元素 方法clear()。  

2. 判断为空 方法empty()返回值为容器的数量是否为空。

3. 交换元素 方法swap()  
使用方法：d1.swap(l2);

#### vector, list, queue的选择

算法竞赛中这三个基本上就用vector（逃    

- 如果你需要高效的随即存取，而不在乎插入和删除的效率，使用vector
- 如果你需要大量的插入和删除，而不关心随即存取，则应使用list
- 如果你需要随即存取，而且关心两端数据的插入和删除，则应使用deque。
- 在搞不清状态时，通常vector是最佳选择。

#### string

string类是C++ STL提供的字符串类。  

string并不是序列式容器，但是这里也一并讲了。

（C++的字符串处理能力很弱，这个string类几乎感觉就是vector &lt;char&gt;）

不过即便如此，用string还是会比char数组方便许多。

要使用string类，应声明下列头文件：
```cpp
#include <string>
```

这里需要注意区分头文件&lt;string&gt;和&lt;cstring&gt;，前者是C++ STL的字符串类，后者是C语言的字符串处理函数头文件，在C语言中声明为&lt;string.h&gt;

**- string对象的定义和初始化**

```cpp
string s; // 产生空的deque

string s(string s2); // 生成一个复制了s2的所有元素的s

string s(int n); // 生成一个含有n个空元素的的string

string s(int n, T e); // 生成一个含有n个值为e的元素的string

string s(itr beg, itr end); //产生一个string，以区间[beg,end]为元素初值

string s = "ojs";
```

**- string的大小和容量**

string提供了size()方法返回当前字符个数，并提供了max_size()方法返回所能容纳的最大字符个数。

string和vector一样，字符个数和容量往往不一样，用capacity() 方法返回string当前容量。

同时，string还提供了与size()方法效果相同的length() 方法。

string的max_size同样非常大，在算法竞赛中一般不需要关心。

**- string中字符的访问**

```cpp

char& s[int idx];

char& s.at(int idx);

char& s.front();

char& s.back();
```

**- string中字符的遍历**

string支持下标遍历和迭代器遍历：  

```cpp
for (int i = 0; i < s.size(); i++)
  cout<< s[i]<< endl;

string::iterator itr;
for (itr = s.begin(); itr != s.end(); itr++)
  cout<< *itr<< endl;
```

**- string中字符的插入**

string类提供了较为丰富的插入方法，不仅像前面的容器那样支持用迭代器指明插入位置，也支持用下标指明插入位置。

```cpp
void s.push_back(char e);

string& s.append(string/char* e); //返回添加后的整个字符串;

string& s.insert(int pos, string/char* e);

string& s.insert(int pos, string/char* e, int n); //在pos插入e的前n个字符;

string& s.insert(int pos, string/char* e, int p2, int n); //在pos插入e从p2开始的n个字符;

string& s.insert(int pos, int n, char e); //在pos插入n个e;

itr s.insert(itr pos, char e);

itr s.insert(itr pos, int n, char e);

itr s.insert(itr pos, itr beg, itr end);
```

**- string中字符的删除**

```cpp
void s.clear();

string& s.erase(int pos);

string& s.erase(int pos, int n);

itr s.erase(itr pos);

itr s.erase(itr beg, itr end);
```

**- string中字符的查找**

string类提供了用以查找字符或子串的方法。  

可以使用方法find()进行正向查找，也可以使用方法rfind() 进行逆向查找。  

如果查找成功，返回第一个字符的下标，反之返回s.npos;

string::npos的类型为size_type，是某个无符号的类型，将string::npos类型转换为int后值为-1。

同样的，下列参数和返回值为方便起见用int替代size_type。

```cpp
int s.find(string/char* e);

int s.find(char s);

int s.find(string/char* e, int pos); //从pos开始找e;

int s.find(char e, int pos);

int s.find(char e, int pos, int n); //从pos开始找e的前n个;
```
rfind() 方法和find() 方法用法类似，这里不再给出。


**- string中的操作符**

string允许通过比较运算符进行字符串的字典序比较，并且允许通过+和+=操作符进行字符串的连接。

**- string中元素的拷贝**

```cpp
string& s.assign(itr beg, itr end);

string& s.assign(int n, char e);
```

**- string中的其他方法**

```cpp
int s.compare(string s2); //字典序比较，s<s2返回负数，s=s2返回0，s>s2返回正数;

int compare(int pos, int n, string/char* s);//比较当前字符串从pos开始的n个字符组成的字符串与s的大小;

int compare(int pos, int n, string/char* s, int pos2, int n2); //比较当前字符串从pos开始的n个字符组成的字符串与s中;

bool s.empty();

string substr(int pos = 0, int n = npos); //返回pos开始的n个字符组成的字符串;
```

#### pair

pair模板类用来绑定两个对象为一个新的对象。

要使用pair需要声明头文件：  
```cpp
#include <utility>
```

**- pair对象的定义和初始化**

```cpp
pair <T1, T2> p;

pair <T1, T2> p(v1, v2);

make_pair(v1, v2);
```

pair的first和second属性(成员变量)分别为第一个和第二个的值。

**- pair的运算符**

```cpp
p1 < p2; //如果p1.first<p2.first或者!(p2.first < p1.first)&& p1.second<p2.second，则返回true

p1 == p2 //p1.first == p2.first && p1.second == p2.second
```

#### set/multiset

set和multiset容器是C++ STL提供的集合类容器，不支持随机访问，通过在内部维护一种称之为“红黑树”的二叉排序树，从而使得其能达到较高的查找和删除效率。

set和multiset的区别在于，set中相同值的元素只能存储一个，而multiset则能存储重复值的元素。

set和multiset容器的方法相同，下面统一以set做说明。

要使用这两个容器，需要声明头文件：

```cpp
#include <set>
```

**- set容器的声明**

```cpp
template <class Key, class Traits = less<Key>, class Allocator = allocator<Key> > class set;
```

其中参数K代表的set中元素的数据类型，Traits是用于实现集合内部排序的仿函数，如果不声明默认为less&lt;Key&gt;，Allocator代表集合的内存分配器，默认为allocator&lt;Key&gt;。

一般在算法竞赛中，最后一个模板参数Allocator不需要考虑，第二个参数Traits用到的地方也比较少，但是在某些时候（比如Key的类型是自定义的类型）的时候，或者希望从大到小排的时候，就需要用到。

**- set对象的定义和初始化**

```cpp
set <T> s;

set <T> s(itr beg, itr end);

set <T, functor> s(functor f);

set <T, functor> s(itr functor f)
```

一般来说，如果遇到简单数据类型需要降序排列的话，可以定义为：

```cpp
set <T, greater <T> > st;
```

如果遇到结构体排序，可以用之前的重载运算符的方法，也可以给set类模板传参数：  

1. 可以传函数指针：  
```cpp
bool comp(int x, int y) {return x < y;}
typedef bool(* pass)(int, int);  
set <int, pass> st(comp);
```
2. 可以传lambda表达式：  
lambda表达式是C++ 11加入的匿名函数，在一些场合使用会更方便。  
```cpp
auto func = [] (int x, int y) {return x < y;};
set <int, decltype(func)> st(func);
```
3. 可以传仿函数（函数对象）：  
```cpp
class comp
{
  public: bool operator () (int x, int y) const {return x < y;}
};
set <int, comp> st;
```

**- set容器的大小**

同序列式容器一样，可以通过size() 方法获取元素个数，通过max_size() 方法获取能容纳的最大元素数量。

**- set中元素的访问/查找**

set不支持随机访问，亦不支持首尾访问，但是提供了查找方法find() 进行元素查找，亦可通过迭代器访问。

```cpp
  itr s.find(T e); //如果有返回元素对应的迭代器位置，没有返回s.end();
```

同时，set还支持一些其他的查找方法
```cpp
pair<itr, itr> s.equal_range(T e); //返回键值等于e的元素区间;

int s.count(T e); //返回键值为e的元素的个数;

itr s.upper_bound(T e); //返回大于e的第一个元素位置;

itr s.lower_bound(T e); //返回大于等于e的第一个元素位置;
```

**- set中元素的遍历**

set容器可通过迭代器遍历元素

```cpp
set <int>::iterator itr;
for (itr = s.begin(); itr != s.end(); itr++)
  cout<< *itr<< endl;
```
**- set中元素的插入**

```cpp
pair<itr, bool> s.insert(T e); //插入e。返回e的位置和是否成功插入

void s.insert(itr beg, itr end);
void s.insert(init_list l); //C++ 11标准方法
```

**- set中元素的删除**

```cpp
void s.clear();

int s.erase(T e); //返回删除元素的个数
```

**- set中的其他方法**

```cpp
bool s.empty();
```

**- 用于set容器的函数**

- set_union() 求两个set的并集
- set_intersection() 求两个set的交集
- set_difference() 求第一个相对于第二个的差集

用法如下：  
```cpp
s3.end() set_union(s1.begin(), s1.end(), s2.begin(), s2.end(), s3.begin());
```

#### map/multimap

map和multimap是C++ STL提供的键值对应的集合，很多用法和set/multiset很像，这里给出一些不同点。  

使用map/multimap需要声明头文件：  
```cpp
#include <map>
```

**- map容器的声明**

```cpp
map <T Key, T Value, Functor Traits = less<Key>, Functor Allocator = allocator<pair<const Key, Value> > >;
```

map中前面一个模板参数放Key(键)，也就是通过键来进行排序，然后比set多了一个Value（值），可以通过键找到值。

**- map中元素的遍历**

map的迭代器（iterator）相当于一个pair有first和second两个属性（成员变量）。
```cpp
cout<< itr->first<< " "<< itr->second<< endl;
```

**- map中元素的查找**

map中通过find()等方法返回的是迭代器，需要用itr->second获取值。

map重载了下标运算符[]，可以通过下标下标运算符直接获取值。

```cpp
Tv a = m[Tk e];
```

需要注意的是，用下标运算符通过键获取值建议只获取在map中已经存在的键对应的值。

**- map中元素的插入**

map通过insert()方法插入要使用pair

```cpp
s.insert(pair<Tk, Tv>(k, v));

s.insert(make_pair(k, v));

s.insert({k, v}); //C++ 11;

s.insert({{k1, v1}, {k2, v2}});//C++ 11
```

map也可以通过下标运算符[]插入：  

```cpp
m[Tk k] = v;
```

#### hashmap(set)/unordered_map(set)

前面的map和set内部是用红黑树实现的，这里的这几个容器内部用散列（哈希）表实现的，hashmap(set)并非C++标准容器，而是各编译器厂商自行实现的，而unordered_map(set)为C++ 11标准加入的标准STL容器。  

这里不再详细讲述这几种容器。

#### bitset
bitset是一个用于存储二进制位的容器，就像bool数组一样，但是有空间优化，bitset中的一个元素一般只占1 bit，即0.125个字节。

要使用bitset需要声明头文件：  
```cpp
#include <bitset>
```

**- bitset对象的定义和声明**
 ```cpp
 bitset <int n> b; //n为二进制位数

 bitset <int n> b(int num);
 bitset <int n> b(string s); //s为字符串形式的二进制位
 ```

**- bitsetd的运算**

bitset可以像一个整数一样的进行各种位运算，还可以和整数进行==运算。

bitset支持下标运算符[]的操作，能通过下标获取和修改该位的值。

bitset也可以使用cout输出。

**- bitset的方法**

```cpp
int b.size(); //返回位数

int b.count(); //返回为1的位数个数

bool b.any(); //返回是否有为1的位

bool b.none(); //范围是否没有为1的位

bitset b.set(); //全部位赋值为1

bitset b.set(int p); //将p位赋值为1

bitset b.set(int p, bool num); //将p位设置为num

bitset b.reset(); //全部位赋值为0

bitset b.reset(int p);

bitset b.flip(); //全部取反

bitset b.flip(int p);

unsigned long b.to_ulong(); //返回为ulong，超出报错

unsigned long long b.to_ullong(); //返回为ull，超出报错

string b.to_string(); //返回为string

```

#### stack

stack是C++ STL提供的容器适配器：栈。

若要使用stack，需声明头文件：
```cpp
#include <stack>
```

**- stack对象的定义和初始化**

```cpp
stack <T> s;
stack <T, Container = deque<T> > s;
```

第二个模板参数是定义stack内部储存元素的容器，默认是deque，如果要修改，方法如下：  
```cpp
stack <int, vector <int> > st;
```

所有了序列式容器(vector, list, deque)都支持stack。

**- stack提供的方法**

```cpp
void s.push(); //入栈

void s.pop(); //出栈

T s.top(); //获取栈顶元素

bool s.empty();
int s.size(); //返回栈中元素
```

需要注意的是，stack没有clear() 方法，如果需要清空，需要循环出栈。

#### queue

queue是C++ STL提供的容器适配器：队列。

若要使用queue，需声明头文件：
```cpp
#include <queue>
```

**- queue对象的定义和初始化**

```cpp
queue <T> q;
queue <T, Container = deque<T> > q;
```

vector容器因为无法提供pop_front() 方法因而无法作为queue的内部储存容器。

**- queue提供的方法**

```cpp
void q.push(); //入队

void q.pop(); //出队

T q.front(); //获取队首元素

T q.back(); //获取队尾元素

bool q.empty();
int q.size(); //返回栈中元素
```

queue同样没有提供clear()  方法，如果需要清空需循环出队。

#### priority_queue

priority_queue 是C++ STL提供的容器适配器：优先队列。

何为优先队列？在优先队列中，元素并不是按照顺序存储在容器中的，而是按照优先级顺序存储在容器中的。即在此类队列中，被压入的元素已经按优先级顺序进行了自动排序，默认排序准则是“<”。

优先队列和普通队列一样，使用时需要包含头文件：

```cpp
#include <queue>
```

**- priority_queue对象的定义和初始化**

```cpp
priority_queue <T> q;

priority_queue <T, Container = deque<T> > q;

priority_queue <T, Container = deque<T>, Compare = less <T> > q;
```

list容器因为无法提供随机存取迭代器因而无法作为queue的内部储存容器。

如果需要自定义比较标准，参照set容器。

**- priority_queue提供的方法**

同queue。

### 算法

C++ STL提供了许多会在算法竞赛中用到的算法函数。

要使用这些算法的函数，需要声明头文件：  

```cpp
#include <algorithm>
```

实现时会用到比较的函数一般都会提供自定义比较的方法。

algorithm中提供的方法大体分为4类：

- 非修改式序列算法
- 修改式序列算法
- 排序和相关操作
- 通用数字运算

#### sort()/stable_sort()

sort绝对是算法竞赛中使用次数最多的STL算法库函数之一，此函数提供的基于快速排序，插入排序等排序的算法进行排序的功能，一般情况下比手写的快速排序速度还会快。

```cpp
sort(itr beg, itr end);

sort(itr beg, itr end, func comp);
```

如果是对普通数组进行排序怎么办呢？

```cpp
sort(&arr[i], &arr[j]);

sort(arr, arr + n);
```

一般我们对数组排序都会使用下面的做法，在这里，arr退化为数组首指针，和&arr[0]相同。

对于sort的自定义比较，传自定义比较函数要比类模板传参要简单，如果传全局函数，可以直接传函数名：

```cpp
bool comp(int x, int y) {return x < y;}
sort(arr, arr + n, comp);
```

如果传lambda表达式，则可以直接将表达式写在参数那里：  
```cpp
sort(arr, arr + n, [] (int x, int y) {return x < y;});
```

stable_sort() 能保证在排序时对于比较方法相同元素的相对顺序不发生改变。

什么叫对于比较方法相同呢，比如你自定义了一个结构体和比较函数：

```cpp
stuct node { int x, y; };

bool comp(node a, node b) { return a.x < b.x };
```
比较函数只比较了结构体中x的值，那么，比如说(node){x=1, y=1}就和(node){x=1, y=2}这两个结构体变量就对于比较函数comp相同。

#### max()/min()

max()和min()函数返回两个值中相比较大/小的元素

```cpp
T min(T a, T b);
```

#### max_element()/min_element()

返回序列中较大/小的元素

```cpp
T max_element(itr beg, itr end);

T max_element(itr beg, itr end, func comp);
```

#### reverse()/reverse_copy()

将序列中的元素逆向顺序反转，reverse() 函数直接在源序列反转，reverse_copy() 函数将反转的结果放入一个新的序列。

```cpp
void reverse(itr beg, itr end);

aimItrEnd reverse_copy(souItrBeg, souItrEnd, aimItrBeg);
```

#### rotate()/rotate_copy()

旋转元素

```cpp
void rotate(itr beg, itr newBeg, itr end); //将beg的元素移动到newBeg，其他位置的元素同样按照这种距离移动。

aimItrEnd rotate_copy(souItrBeg, souItrNewBeg, souItrEnd, aimItrBeg);
```

#### swap()

交换两个元素的值。

```cpp
void swap(T a, T b);
```

#### next_permutation()/prev_permutation()

提供全排列算法的函数。

next_permutation() 用于求当前排列的下一个排列（比当前排列大），而prev_permutation() 用于求当前排列的上一个排列（比当前排列小）。

两个函数的返回值均为bool类型，对于next_permutation()，若当前排列为最大排列，返回false，反之返回true，对于prev_permutation()，若当前排列为最小排列，返回false，反之返回true。

```cpp
bool next_permutation(itr beg, itr end);
```

#### lower_bound()/upper_bound()

这两个函数用于实现对已排序的序列的二分查找。

lower_bound()返回第一个大于等于给定值的元素的位置，如果没有则返回最后一个元素之后的位置。

upper_bound()返回第一个大于给定值的元素的位置，如果没有则返回最后一个元素之后的位置。

```cpp
iter lower_bound(iter beg, iter end, T e);
```

#### unique()/unique_copy()

用于移除序列中重复的元素，用以保证序列中相同值的元素唯一。

需要注意的是，unique()的移除是伪移除，只是将重复的值放在序列的末尾，返回去重之后的尾地址。

```cpp
itr unique(itr beg, itr end);

aimItrEnd unique(souItrBeg, souItrEnd, aimItrBeg);
```

#### count()/count_if()

count() 函数提供了进行元素计数，即求得容器中元素的总个数的功能。另外，还提供了条件计数的函数：count_if()。

```cpp
int count(itr beg, itr end, T e); //返回[beg, end]中值为e的元素的个数

int count_if(itr beg, itr end, func comp); //返回使comp返回true的元素的个数
```

#### find()/find_if()

STL算法库提供了用于搜索的算法，find() 函数返回第一个和给定值相同值的元素的位置，而find_if()用于条件搜索。

```cpp
itr find(itr beg, itr end, T e);

itr find_if(itr beg, itr end, func comp);
```

### stringstream

C++ STL提供了stringstream类用于进行数字和字符串之间的转换。  
（C++ 11提供了诸如to_string()之类的更方便的方法，在此不进行叙述）

要使用stringstream类，需要包含头文件：
```cpp
#include <sstream>
```

stringstream的对象通过<<运算符读入需要转换的数字（字符串），再用>>运算符输出转换完成的字符串（数字）。

具体用法如下：  
```cpp
stringstream ssr;
int i = 100;
string s;
ssr << i;
ssr >> s;
ssr.clear();
cout<< s<< endl;
```

需要注意的是，stringstream在下一次使用时需要调用clear() 方法，否则会出现无法预测的输出。

另外，stringstream类的构造效率较低，建议尽量不要频繁定义stringstream类，如果需要频繁使用，可以在更外围定义，之后每次调用前进行clear() 。

## 例题

我们来看点例题：  

### 序列查找

**问题描述**

输入一个n个数的序列和q次询问，每次询问一个数是否在序列中，如果在，输出序列升序排列后所在位置（从1开始）。所有输入数据的值均不会超过10000。

**样例输入**

4 1   
2 3 5 1   
5   

**样例输出**

5 founds at 4  

**解题思路**

根据题目需要对序列进行升序排序，可以使用sort()函数进行升序排序，然后我们可以用STL带的二分查找函数进行询问的查找。
