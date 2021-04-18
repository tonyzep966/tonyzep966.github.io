---
title: Python zip()函数的应用
categories: Python
tags: Python 基础
---

在完成作业时，我需要将两个列表关联并排序，于是便学到了zip函数

<!--more-->

```python
name = [ "Manjeet", "Nikhil", "Shambhavi", "Astha" ]
roll_no = [ 4, 1, 3, 2 ]
marks = [ 40, 50, 60, 70 ]
  
# using zip() to map values
mapped = zip(name, roll_no, marks)
  
# converting values to print as set
mapped = set(mapped)
  
# printing resultant values 
print ("The zipped result is : ",end="")
print (mapped)
```