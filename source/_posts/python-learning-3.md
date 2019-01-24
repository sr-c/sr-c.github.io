---
title: 【Python】文件与异常
tags: [Python, Head First]
date: 2018-10-26 22:31:03
permalink:
categories:
description:
---
<p class="description">Python文件与异常</p>

<!-- more -->

## 输入

Python中的基本输入机制是基于行的：从文本文件向程序读入数据时，一次会到达一个数据行。

### 基本操作

使用open() BIF处理文件中的数据时，会创建一个迭代器从文件向你的代码输入数据行，一次传入一行数据。

```python
the_file = open('sketch.txt')
# Do something with the data
# in "the_file".
the_file.close()
```

指定文件读取的指针seek(0)回到文件起始位置

使用split()根据需要抽取数据行中的各个部分。split()方法返回一个字符串列表，这回赋值至一个目标标识符列表。这成为多重赋值（multiple assignment）:

```python
(role, line_spoken) = each_line.split(":")
```

### 查看帮助

```python
help(each_line.split)
```



## 处理异常的两种方法

### 增加额外的逻辑

```python
import os

if os.path.exists('sketch.txt'):
    data = open('sketch.txt')

    for each_line in data:
	if not each_line.find(':') == -1:
        (role, line_spoken) = each_line.split(':', 1)
        ...
        
else:
    print ('The data file does not exist!')
```

随着必须考虑的错误越来越多，“额外增加的代码和逻辑”方案的复杂性也随之增加，直到最后可能会严格程序的本来作用。

python的基本理念是**要重点关注你的代码需要做什么**。

### 先尝试，然后恢复

**try/except机制**

```python
try:
    your codes
except:
    error handle codes
```

放过错误，可以使用pass语句，忽略这个错误。

但是，过于放松条件，会使得程序悄无声息地忽略关键的错误。因此，应当注意以一种不那么一般化的方式使用except。

**指定要处理的错误类型**

一般，我们真正需要考虑的只是之前开发程序时出现的异常类型，比如IOError和ValueError。因此，我们推荐在except语句中指定要处理的运行时错误类型。

## 参考来源

[《Head First Python》](https://www.oreilly.com/library/view/head-first-python/9781491919521/)

<hr />