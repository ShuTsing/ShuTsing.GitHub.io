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
还比如说，如果你需要频繁的在头尾操作元素，就应该选用容器list，需要频繁进行随机存取就应该使用vector。在合适的情况选择合适的容器。  

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
  int temp = *x;
  *x = *y;
  *y = temp;
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
- 数值计算
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
比如queue，stack这种。个人感觉多半是修改的list的接口。

下面会逐一介绍STL各个容器的用法。

#### vector
说实话我不是很懂为什么C++ STL要把动态数组取名为vector而不是ArrayList，这直接导致我写向量类的时候取名异常纠结。   

使用vector容器需要声明头文件：
```cpp
#include <vector>
```
vector的元素可以是任意类型T，但必须具备赋值和拷贝能力。

**- vector对象的定义和初始化**  
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

总结下来，便是：  
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
include <iterator>

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
include <iterator>

distance(first, last);//返回last - first的距离  
```
需要注意的是，distance()方法只有在诸如vector，deque等能随机访问元素的容器的迭代器上面有较好的效率，即时间复杂度为O(1)，反之，在诸如list，set等不支持随机访问元素的容器的迭代器上面则需要O(n)的时间复杂度来完成距离计算。  

两个同类型的迭代器还能用iter_swap()方法进行交换
```cpp
include <iterator>

iter_swap(itr1, itr2);
```


**- vector中元素的插入**

vector容器提供了push_back()方法用于在尾部插入元素，同时提供了insert()方法进行随机插入。

push_back()的时间复杂度为O(1)。   
insert()的时间复杂度为O(n)。

方法如下：
```cpp
void v.push_back(T e); //在尾部插入e的副本

itr v.insert(int pos, T e); //在pos位置插入元素e的副本，并返回新元素位置

itr v.insert(int pos, int n, T e); //在pos位置插入n个元素e的副本

itr v.insert(int pos, itr beg, itr end); //在pos位置插入区间[beg,end]内所有元素的副本

```
具体用法如下：
```cpp
std::vector<int> v(4, 2);
v.insert(v.begin + 1, 3);
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

itr v.erase(int pos); //删除pos位置的元素，返回下一个元素的位置

itr v.erase(itr beg, itr end); //删除区间[beg,end]内所有元素，返回下一个元素的位置

```

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
**-list对象的定义和初始化**  

```cpp
list <T> l; // 产生空的list

list <T> l(list <T> l2); // 生成一个复制了l2的所有元素的l

list <T> l(int n); // 生成一个含有n个空元素的的list

list <T> l(int n, T e); // 生成一个含有n个值为e的元素的list

list <T> l(itr beg, itr end); //产生一个list，以区间[beg,end]为元素初值
```

**-list中元素的访问**

list容器不支持随机访问，仅提供front()和back()方法访问首尾元素。
```cpp
T& l.front();

T& l.back();
```

**-list中元素的遍历**

未完待续……
