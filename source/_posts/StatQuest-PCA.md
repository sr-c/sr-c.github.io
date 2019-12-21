---
title: StatQuest-PCA
tags: [StatQuest,PCA]
date: 2019-07-19 10:36:28
permalink:
categories:
description:
---
<p class="description"></p>
<!-- more -->

## PCA

主成分分析是处理多维数据时降维的方法。举例说明，不同处理组中上万个基因的表达量矩阵，也就对应着上万个维度。

![](/home/yaman/Pictures/StatQuest/PCA/01_original_line.jpg)

## Step 1

投射Gene1 与Gene2 的表达量。取其中点（重心）为原点，平移图像。在取得最适合的过原点的直线。其到各点的距离(b)和最短，或各点在直线上映射点到原点的距离(c)和最大。

![](/home/yaman/Pictures/StatQuest/PCA/02_PC1_recipe.png)

> 由于
> $$
> a^2 = b^2 + c^2
> $$

计算映射点到原点的距离平方之和
$$
d1^2 + d2^2 + d3^3+d4^2+d5^2+d6^2=sum of squared distances=SS(distances)
$$
取得的这一直线就被称为`principal component one (PC1)`

PC1 的斜率反映了Gene1 与Gene2在PC1上的分布

> how the data are spread out

特征向量

### Terminology Alert!!!

#### linear combination

PC1 is a linear combination of variables



PCA with Singular Value Decomposition (SVD)

#### 特征向量 Singular Vector / Eigenvector

Eigenvalue for PC1



## 词汇表

projected point 映射点

inversely related 负相关

intuitively 直觉地

rotate the line

spread out

Pour over ice and serve!

proportion

>  e.g. proportions of each gene are called loading scores.

perpendicular 垂直的

approximation 近似

informative 有信息的

substantial 相当可观的

## 参考来源

https://www.youtube.com/watch?v=FgakZw6K1QQ

https://en.wikipedia.org/wiki/Eigenvalues_and_eigenvectors

<hr />