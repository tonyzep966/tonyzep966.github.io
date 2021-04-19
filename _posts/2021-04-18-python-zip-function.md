---
title: Python zip()函数的应用
categories: Python
tags: Python 基础
---

zip函数是Python的内置函数

<!-- more -->

笔者一直没有系统地学习过Python，因此这第一篇涉及技术的博客也是现学现用的记录。
在最近的一次作业里，我学到了这样一种用法：
```python
list1_sorted, list2_sorted = zip(*sorted(zip(list1, list2), key=operator.itemgetter(0), reverse=False))
```
我希望两个列表list1和list2都被排序，且其中的第二个列表排序结果受第一个列表的影响。例如有：
```python
list1=[5, 2, 4, 3, 1]
list2=["five","two","four","three","one"]
```
则排序后的结果应为:
```python
def zip(*iterables):
    # zip('ABCD', 'xy') --> Ax By
    sentinel = object()
    iterators = [iter(it) for it in iterables]
    while iterators:
        result = []
        for it in iterators:
            elem = next(it, sentinel)
            if elem is sentinel:
                return
            result.append(elem)
        yield tuple(result)
```