---
title: Python练习-3
tags: [Python, Rosalind]
date: 2018-10-31 21:59:15
permalink:
categories:
description:
---
<p class="description">在Rosalind上进行练习</p>

<!-- more -->

## 斐波那契数列

http://rosalind.info/problems/fib/

数列的循环数为n(≤40)，扩大级数为k(≤5)

F~1~ = F~2~ = 1

Fn=Fn−1+Fn−2

```python
def fib(i,k):
    i = int(i)
    if i in [1,2]:
        return 1
    elif i > 2:
        return k * fib(i-2,k) + fib(i-1,k)
    else:
        print('Not a positive integer.')

with open('rosalind_fib.txt') as f:
    (n, k) = f.readline().strip().split(' ')
    n = int(n)
    k = int(k)
    print(fib(n,k))
```

## 孟德尔第一定律

http://rosalind.info/problems/iprb/

```python
def c2(num):
    num = int(num)
    return num * (num - 1) / 2

with open('rosalind_iprb.txt') as f:
    (k, m, n) = f.readline().strip().split(' ')
    total_num = int(k) + int(m) + int(n)
    total_count = c2(total_num)
    recessive_count = c2(m) / 4 + int(m) * int(n) / 2 + c2(n)
    print(1- recessive_count/total_count)
```

## 参考来源

<hr />