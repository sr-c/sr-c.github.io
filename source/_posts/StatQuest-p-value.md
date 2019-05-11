---
title: 【StatQuest】P值
tags: [StatQuest]
date: 2018-12-08 16:52:45
permalink:
categories:
description:
---
<p class="description">什么是p值</p>

<!-- more -->

许多人认为p值代表了可能性/概率(probability)，p-value确实与probability相关，但并不是一个意思。举例如下

## 抛硬币

**试验**：已有一枚均匀的硬币，抛硬币两次。

**提问**：试验中我们得到两次正面的概率是多少，得到两次正面的p值是多少？

每次抛硬币，我们得到正面(head, H)与反面(tail, T)的概率都是0.5, 因此两次抛硬币我们有4种可能性，而且是等概率的。

> HH, HT, TH, TT

因此，我们计算两次正面(HH)概率的方法就是
$$
\frac{HH发生的次数}{总事件数} = \frac{HH}{HH, HT, TH, TT} = \frac{1}{4} = 0.25
$$
两次反面(TT)的概率同理，但一次正面一次反面的概率如何呢？

$$
\frac{HT或TH发生的总次数}{总事件数} = \frac{HT, TH}{HH, HT, TH, TT} = \frac{2}{4} = 0.5
$$

**注意**：这种情况下是我们并不在意事件发生的顺序，这在试验抽样时也常常如此。我们并不在意抽样时的顺序。

## 试验的例子

试验：假设H代表一个基因的等位基因(allele), T代表另一个等位基因。若试验中父本与母本的这个基因都是是杂合子(heterozygote)

提问：

1. 求后代的基因型。

2. 若母本是H的纯合子，求后代的基因型。

这个事件的概率与抛两次硬币的概率类似。

## p-value

> A p-value is the probability that random chance generated the data, or something else that is equal or rarer.

根据上述定义，p-value 由三部分组成，随机过程产生目标数据的概率，等同于该事件概率的其他事件，以及比目标事件更小概率发生的事件。这三者之和构成了该事件的p-value

- Part1:  A p-value is the probability that random chance generated the data
- Part2: or something else that is equal
- Part3: or rarer

计算HH发生的p值时，这三个部分如下：

Part1：P(HH) = 0.25

Part2:  P(TT) = 0.25

Part3: 0

因此，p-value = 0.25 + 0.25 + 0 = 0.5

**BAM!**

## 抛5次硬币

`HHHHH`的概率为1/32 = 0.0375，而p-value则是$1/32 + 1/32 + 0 = 1/16 = 0.0625 > 0.05$. 此例中，虽然发生的概率小于0.05, 但p-value大于0.05, 因此我们认为事件`HHHHH`的发生并不是unusual

`HHHHT`的概率为5/32,（不考虑事件发生的顺序）, p-value为$5/32 + 5/32 + (1/32 + 1/32) = 3/8 = 0.375$

## 测量身高

身高的密度分布为正态分布，举例如下

![019-03-16 (2)](C:\Users\Yaman\OneDrive\图片\屏幕快照\2019-03-16 (1).png)

测量一个人的身高在142 cm的p-value是0.025(half of the "equal to or rarer") + 0.025(the other half of the "equal to or rarer") = 0.05

测量一个人的身高在155.4~156 cm之间的p-value是 0.04 + 0.48 + 0.48 = 1, 因此即使测量出这个事件的概率很低(0.04)，但其p-value为1，意味着发生这件事并不特殊。

> There's nothing special about measuring someone who has the average height even though that particular event is relatively rare.

**DOUBLE BAM!**

## 参考来源

https://www.youtube.com/watch?v=5Z9OIYA8He8&list=PLblh5JKOoLUK0FLuzwntyYI10UQFUhsY9&index=16

<hr />