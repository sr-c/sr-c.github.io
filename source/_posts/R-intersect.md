---
title: 【R】集合运算
tags: [R,集合运算,交集,并集]
date: 2018-10-14 22:56:14
permalink:
categories:
description:
---
<p class="description">取多个变量的交集与并集</p>

<!-- more -->

## 集合运算

```R
x <- c(1,3,4)
y <- c(2,4,8)
union(x,y) #取向量x与向量y的交集，并去冗余
intersect(x,y) #取向量x与向量y的并集
setdiff(x,y) #找出向量x中与向量y不同的元素
setequal(x,y) #判断x,y是否相同
x %in% y #判断x中的每个元素是否在y中，返回逻辑向量
```

## 简便方案

对于简单的两组列表，使用`sort`与`uniq`联用就可达到目的

```bash
sort a.txt b.txt | uniq -d  #求交集
sort a.txt b.txt | uniq #求并集
sort a.txt b.txt b.txt | uniq -u #求差集(A-B)
sort a.txt b.txt | uniq -u #求对称差，即A,B中只出现一次的项
```



## 参考来源

https://blog.csdn.net/sinat_26917383/article/details/51277581

https://blog.csdn.net/woodcorpse/article/details/80494605

https://blog.csdn.net/yinxusen/article/details/7450213

<hr />