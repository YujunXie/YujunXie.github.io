---
title: C++之STL(4)：探索algorithm
key: 20171129
tags: C++
---

algorithm是C++的标准模版库（STL）中最重要的头文件之一，提供了大量基于迭代器的非成员模版函数。

所有函数参考：[algorithm](http://www.cplusplus.com/reference/algorithm/)

重点看下排序：

~~~
sort	        对给定区间所有元素进行排序
stable_sort	对给定区间所有元素进行稳定排序
partial_sort	对给定区间所有元素部分排序
partial_sort_copy	对给定区间复制并排序
is_sorted	判断一个区间是否已经排好序
is_sorted_until 找到第一个未排好序的元素
nth_element	找出给定区间的某个位置对应的元素
~~~

自带比较函数的排序：

    bool compare(int a,int b)
    {
          return a<b;
    }
    sort(begin,end,compare);

functional还提供了一堆基于模板的比较函数对象。它们是（看名字就知道意思了）：`equal_to<Type>`、`not_equal_to<Type>`、`greater<Type>`、`greater_equal<Type>`、`less<Type>`、`less_equal<Type>`。

升序：`sort(begin,end,less<data-type>());`
降序：`sort(begin,end,greater<data-type>());`

qsort是`stdlib.h`头文件里的排序函数：

    int cmp(int a,int b){
              return a<b;
    }
    qsort( 数组名 ，元素个数，元素占用的空间(sizeof)，cmp);

实现的算法为快速排序。

sort和qsort都只能对连续内存的数据进行排序。
