---
title: 【R】改变因子的排列顺序
tags: [R]
date: 2018-09-16 15:38:13
permalink:
categories:
description:
---
<p class="description">在可视化时，我们往往需要改变因子展示的顺序</p>

<!-- more -->

R中的因子存在着有序和无序两种，默认按照ASCII顺序排序。

## 对于无序因子

使用levels函数指定顺序，如下

```R
# 创建一个错误次序的因子 
sizes <- factor(c("small", "large", "large", "small", "medium")) 
sizes 
#> [1] small large large small medium 
#> Levels: large medium small
# 顺序被直接指定
sizes <- factor(sizes, levels = c("small", "medium", "large")) 
sizes 
#> [1] small  large  large  small  medium 
#> Levels: small medium large
```

## 对于有序因子

```R
sizes <- ordered(c("small", "large", "large", "small", "medium")) 
sizes <- ordered(sizes, levels = c("small", "medium", "large")) 
sizes 
#> [1] small large large small medium 
#> Levels: small < medium < large
```

## 小tips

快速逆序排列

```R
sizes <- factor(sizes, levels=rev(levels(sizes)))
```

## 参考来源

http://www.cookbook-r.com/Manipulating_data/Changing_the_order_of_levels_of_a_factor/

https://www.jianshu.com/p/87ae057ae557

<hr />