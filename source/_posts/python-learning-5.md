---
title: 【Python】理解数据
tags: [Python, Head First]
date: 2018-10-28 22:12:13
permalink:
categories:
description:
---
<p class="description">处理数据</p>

<!-- more -->

## 方法串链与函数串链

方法串链从左向右读

```python
f.readline().strip().split('.')
```
函数串链从右向左读
```python
print(sorted(james))
```

## 排序有两种方式

**原地排序**（In-place sorting）是指按照指定的顺序排列数据，然后用排序后的数据替换原来的数据。原来的顺序会丢失。`sort()`方法提供原地排序。

**复制排序**（Copied sorting）是指按照指定的顺序排列顺序，然后返回原数据的一个有序副本。原数据的顺序依然保留，只是对一个副本排序。`sorted()` BIF支持复制排序。

## 数据清洗

由于输入的数据常常并不是理想的标准格式，因此我们需要对其进行清洗。

例如，教练记录的选手跑步成绩记录成了如下三种形式

> 2-15, 3:04, 2.44

需要先将这些数据统一为同一种计时方式，再进行后续处理。

> 创建函数sanitize()，从各个选手的列表接收一个字符串作为输入，然后处理这个字符串，将找到的所有短横线或冒号转换为一个点号，并返回清理过的字符串。

```python
def sanitize(time_string):
    if '-' in time_string:
        splitter = '-'
    elif ':' in time_string:
        splitter = ':'
    else:
        return(time_string)
    (mins, secs) = time_string.split(splitter)
    return(mins + '.' + secs)
```



## 列表推导

列表推导（list comprehension）是Python提供的工具，用于方便地转换列表，减少代码量。

```python
clean_mikey = []

for each_t in mikey:
    clean_mikey.append(sanitize(each_t))
```

```python
clean_mikey = [sanitize(each_t) for each_t in mikey]
```

## 迭代删除重复项

```python
james = sorted([sanitize(t) for t in james])
unique_james = []
for i in james:
    if i not in unique_james:
        unique_james.append(i)
        
print(unique_james[0:3])
```

## 使用集合删除重复项

工厂函数set() BIF创建**集合**，其中的数据项是无序的，而且不允许重复。

```print
print(sorted(set([sanitized(i) for i in james]))[0:3])
```



## 参考来源

[《Head First Python》](https://www.oreilly.com/library/view/head-first-python/9781491919521/)

<hr />