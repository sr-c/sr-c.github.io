---
title: Python练习-1
tags: [Python, Rosalind]
date: 2018-10-29 21:41:24
permalink:
categories:
description:
---
<p class="description">在Rosalind上进行练习</p>

<!-- more -->

## 获得一条序列的反义链

http://rosalind.info/problems/revc/

```python
with open('rosalind_revc.txt') as string_dna:
    string_dna = string_dna.readline().strip()
    new_sting = []
    for i in string_dna:
        if i == 'A':
            new_sting.append('T')
        elif i == 'C':
            new_sting.append('G')
        elif i == 'T':
            new_sting.append('A')
        elif i == 'G':
            new_sting.append('C')

    #将列表倒序排列，revesre()函数进行原为改变，此时不能够将其赋值给另一个列表
    new_sting.reverse()
    #将列表转换为字符串
    rev_string = ''.join(new_sting)

print(rev_string)
```

### 倒置列表的三种方法

#### 使用reverse()方法

```python
list.reverse()
```

注意，reverse()方法对列表进行原位改变，此时不能够对另一个函数赋值，否则会得到空值。

```python
>>> a = [5,7,6,3,4,1,2]
>>> b = a.reverse()
>>> print b
None
```

类似地，sort()方法也会出现如此情况，此时，应当使用sorted()函数。

#### 使用reversed()函数

```python
a=[1,2,3,4,5,6,7,8,9]
b=list(reversed(a))
print b
```

注意：reversed()函数返回的是一个迭代器，而不是一个列表，需要再使用List函数转换一下。

#### 使用切片

```python
L[::-1]
```

### 将列表转换为字符串

使用join()方法

```python
<str> = <separator>.join(<list>)
```

join方法是字符串的方法，因此要求`.`之前必须为str类型

## 计算GC含量

http://rosalind.info/problems/gc/

输入含有10条核算序列的FATSA格式文件，每条序列约长1kbp.

输出GC含量最高的序列ID与其对应的GC含量

```python
try:
    fasta = {}
    with open('rosalind_gc.txt') as lines:
        for line in lines:
            line = line.strip()
            if line[0] == '>':
                name = line[1:]
            else:
                fasta[name] = fasta.get(name, '') + line

except IOError as err:
    print('File error: ' + str(err))

max_gc = 0
max_id = ''
#使用for循环取得最大的GC含量
for read_id in fasta.keys():
    rate_gc = (fasta[read_id].count('G') + fasta[read_id].count('C')) / len(fasta[read_id])
    if rate_gc > max_gc:
        max_gc = rate_gc
        max_id = read_id

print(max_id)
print(max_gc * 100)
```

上述程序要求输入的数据中，不含有N碱基，也不含有小写碱基。

## 参考来源

http://www.iplaypy.com/jinjie/jj114.html

https://www.jianshu.com/p/c61279736a03

https://blog.csdn.net/akisayaka/article/details/50042175

https://blog.csdn.net/u013810296/article/details/57082163?utm_source=blogxgwz1

<hr />