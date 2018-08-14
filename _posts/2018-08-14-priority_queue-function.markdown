---
layout:     post
title:      "CodeBlocks17.12编译器priority_queue比较函数问题"
subtitle:   "Compile problem of priority_queue in CodeBlocks"
date:       2018-08-14 20:48:00
author:     "ShuTsing"
header-img: "img/post-bg-31.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - STL
---

巨毒瘤……

刚刚打了多校第7场，补K题的时候，我的解法，要用K个堆，每个堆以ki为关键字。

[HDU-6396 Swordman](http://acm.hdu.edu.cn/showproblem.php?pid=6396)

我用priority_queue来写这玩意，因为每个堆放的类型一样而比较的关键字不一样，于是写出了下面的程序：

```cpp
    struct monstr {
        int ai[6];
        int bi[6];
    };
    using func_type = function <bool (monstr&, monstr&)>;
    using monstr_priority_queue = priority_queue <monstr, vector <monstr>, func_type>;
    monstr_priority_queue pq[6];
void init_func()
{
    for (int i=0;i<6;i++)
    {
        auto func = [=](monstr& a, monstr& b) {return a.ai[i] > b.ai[i];};
        pq[i] = monstr_priority_queue(func);
    }
    return;
}
```

通过lambda函数很巧妙的给k个堆传入了不同的比较函数（要在C++11及以上标准），然后编译……失败？？？

![kizuna](https://upload-images.jianshu.io/upload_images/13252077-918a3447311007d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

编译失败信息：

```cpp
predefined_ops.h||In instantiation of 'constexpr bool __gnu_cxx::__ops::_Iter_comp_iter<_Compare>::operator()(_Iterator1, _Iterator2) [with _Iterator1 = monstr; _Iterator2 = monstr; _Compare = std::function<bool(monstr&, monstr&)>]':|
functional|1982|  required by substitution of 'template<class _Res, class ... _ArgTypes> template<class _Functor> using _Invoke = decltype (std::__callable_functor(declval<_Functor&>())((declval<_ArgTypes>)()...)) [with _Functor = __gnu_cxx::__ops::_Iter_comp_iter<std::function<bool(monstr&, monstr&)> >; _Res = bool; _ArgTypes = {monstr&, monstr&}]'|
functional|1992|  required by substitution of 'template<class _Res, class ... _ArgTypes> template<class _Functor> using _Callable = std::__and_<std::function<_Res(_ArgTypes ...)>::_NotSelf<_Functor>, std::__check_func_return_type<std::function<_Res(_ArgTypes ...)>::_Invoke<_Functor>, _Res> > [with _Functor = __gnu_cxx::__ops::_Iter_comp_iter<std::function<bool(monstr&, monstr&)> >; _Res = bool; _ArgTypes = {monstr&, monstr&}]'|
functional|2057|  required by substitution of 'template<class _Functor, class> std::function<_Res(_ArgTypes ...)>::function(_Functor) [with _Functor = __gnu_cxx::__ops::_Iter_comp_iter<std::function<bool(monstr&, monstr&)> >; <template-parameter-1-2> = <missing>]'|
predefined_ops.h|130|required from 'constexpr __gnu_cxx::__ops::_Iter_comp_iter<_Compare> __gnu_cxx::__ops::__iter_comp_iter(_Compare) [with _Compare = std::function<bool(monstr&, monstr&)>]'|
stl_heap.h|385|required from 'void std::make_heap(_RandomAccessIterator, _RandomAccessIterator, _Compare) [with _RandomAccessIterator = __gnu_cxx::__normal_iterator<monstr*, std::vector<monstr> >; _Compare = std::function<bool(monstr&, monstr&)>]'|
stl_queue.h|414|required from 'std::priority_queue<_Tp, _Sequence, _Compare>::priority_queue(const _Compare&, _Sequence&&) [with _Tp = monstr; _Sequence = std::vector<monstr>; _Compare = std::function<bool(monstr&, monstr&)>]'|
7777777.cpp|14|required from here|
predefined_ops.h|123|error: no match for 'operator*' (operand type is 'monstr')|
predefined_ops.h|123|error: no match for 'operator*' (operand type is 'monstr')|
```

我去，这都什么鬼，不科学啊，然后转到Dev C++编译，通过……

然后我发现，在code blocks里面，如果编译选项选的C++11则可以编译，但是如果选的C++14或者C++17的话则不能……

然后我换了编译器，在C++14和C++17上面也可以编译……

交到hduoj，也可以编译……

这就很扯淡了，不过code blocks带的这个编译器也不是第一次扯淡了，还有一次死活都不出正确结果（没有未定义行为），逼我改到Dev C++……
