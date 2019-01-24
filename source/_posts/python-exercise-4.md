---
title: Python练习4
tags: [Python, Rosalind]
date: 2018-11-07 20:45:52
permalink:
categories:
description:
---
<p class="description">在Rosalind上进行练习</p>

<!-- more -->

## 计算后代显性表型的期望

http://rosalind.info/problems/iev/

```python
import os
with open('rosalind_iev.txt','r') as f:
    couplesNumb = f.readline().strip()
    [p1, p2, p3, p4, p5, p6] = couplesNumb.split()

dominantNumb = (int(p1)+ int(p2)+ int(p3))*2 + int(p4)*1.5 + int(p5)
print(dominantNumb)
```



## 计算蛋白对应的mRNA序列数量

http://rosalind.info/problems/mrna/

```python
aa_dict = {'A':4,'R':6,'N':2,'D':2,'C':2,'Q':2,'E':2,'G':4,'H':2,'L':6,'I':3,'K':2,'M':1,'P':4,'F':2,'S':6,'T':4,'W':1,'Y':2,'V':4}
total_mrna = 1

with open('rosalind_mrna.txt') as f:
    strand = f.readline().strip()
    for i in range(len(strand)):
        total_mrna *= aa_dict[strand[i]]

print((total_mrna * 3) % 1000000)
```



## 参考来源

<hr />