---
title: 【StatQuest】置信区间
tags: [StatQuest]
date: 2019-03-16 16:54:18
permalink:
categories:
description:
---
<p class="description">置信区间是怎么回事</p>

<!-- more -->

## bootstraping

通过bootstrap来计算置信区间，帮助我们更好地理解置信区间。

假设我们需要测量雌性小鼠的体重，对总体抽样12次，测得12个样本。

**Sampling with replacement:** bootstrap的过程就是重复对这12个样本抽样。

重复这一抽样过程许多次，如10000次，每次都计算抽样后的均值。

那么95% 置信区间覆盖就是这10000个均值中95%的取值范围。

## Confidence interval 的意义

这是一个可视化的统计值，在置信区间外的取值就相对不可信。

假设这12个样本均值的置信区间为(21, 31)，那么所有雌鼠体重的“真实”均值小于20 的p-value一定小于0.05，这不太可能。或者说，雌鼠体重的均值与小于20的数值之间存在显著差异。

> This is unlikeyly and beacuase fo this, we can say there is a statistically significant difference between the true mean and any value less than 20.

## Compare two samples

如果两批样本均值的置信区间没有重叠，那么这两组样品之间一定存在显著差异。

### One Caveat

如果两组样本均值的置信区间存在重叠，它们也可能存在显著差异。只是这时，我们必须做t-检验来判定了。



## 参考来源

<hr />