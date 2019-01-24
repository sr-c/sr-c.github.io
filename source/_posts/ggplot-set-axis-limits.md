---
title: 【ggplot】设置坐标轴区域
tags: [ggplot]
date: 2019-01-17 16:10:26
permalink:
categories:
description:
---
<p class="description">设定坐标轴阈值</p>

<!-- more -->

## 问题

```R
require(ggplot2)
d <- data.frame(x=c(0, 0.002, 0.00575), y = 1:3)
p <- ggplot(d, aes(x, y)) + geom_point() + xlab(NULL) + ylab(NULL)
print(p)
```

![img](https://guangchuangyu.github.io/blog_images/Bioconductor/ggtree/geom_text_files/figure-markdown_strict/unnamed-chunk-1-1.png)

x轴最右端的文本0.006最后一个字符有一半过界了。

## 解决方案

### 设定xlim

```R
p + xlim(NA, 0.0062)
```

### 设定scale_x_continuous

```R
p + scale_x_continuous(limits = c(0, 0.0062), expand = c(0,0))
```

在默认设置下，`expand`对于连续型变量， 会在取值区间的两侧添加5%的范围；对于离散型变量，会在取值区间的两侧添加0.6个单位。

### 冲突

若同时设置xlim与scale_x_continuous，则后者会覆盖前者的设置。

## 参考来源

https://guangchuangyu.github.io/cn/2017/04/ggplot2-overflow/

<hr />