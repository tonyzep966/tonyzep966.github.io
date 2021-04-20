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
[1, 2, 3, 4, 5]
["one", "two", "three", "four", "five"]
```
我们知道`sorted()`函数接受任意类型的可迭代对象，返回一个排序好的列表。`key=`指定带有单个参数的函数，用于从可迭代对象的每个元素中提取用于比较的键。这里的`operator.itemgetter(0)`表示获取待排序对象第零维度（即首位）的元素。
那么，这里就引出了`zip()`函数。`zip()`函数接受多个可迭代对象作为参数，返回一个元组的迭代器，其中的第 i 个元组包含来自每个参数序列或可迭代对象的第 i 个元素。
这里使用官方文档的例子来说：
```python
>>> x = [1, 2, 3]
>>> y = [4, 5, 6]
>>> zipped = zip(x, y)
>>> list(zipped)
[(1, 4), (2, 5), (3, 6)]
```
可以看出，`zip()`函数将两个列表的元素依次“打包成了”元组。结合逐行源码学习一下
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
`zip()`先将参数转换为迭代器列表，然后依次迭代一次，如果迭代出的结果不为空（判断elem是否为sentinel）则将该结果加入result列表; 如果为空，便直接返回空值，这样你不必担心传入该函数的可迭代对象元素个数不相等，因为`zip()`函数这么做就会舍弃掉不相等个数之后的元素，如果你希望保留那些元素，可以考虑使用`itertools.zip_longest()`。在函数的最后，可以看到函数使用yield返回一个result的元组，这是为迭代提供了便利。同时，我们也知道了`zip（）`函数其实是一个“生成器”，而关于yield的学习，我想会在下一篇博客中进行。