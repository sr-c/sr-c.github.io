---
title: 绘制treemap
tags: [treemap, treemapify, R]
date: 2018-12-31 21:04:07
permalink:
categories:
description:
---
<p class="description">使用treemap展示数据</p>

<!-- more -->

## treemap

### 安装

```R
install.packages("treemap")
```

### 读取数据

```R
contig_length <- read.table("genome_length.txt", header = FALSE, sep = '\t')
```

### 画图

```R
treemap(contig_length, index = "V1", vSize = "V2", palette = "Reds", title = "Contig length of xxx genome")
```

指定每格的颜色与边框宽度

```R
#添加一列作为颜色列，为每一行指定颜色
OS_contig_length$V3 <- rep("#ffffff", 23)
#指定type为color,不显示index
treemap(OS_contig_length, index = "V1", vSize = "V2", vColor = "V3", type = "color", border.lwds = 1, title = "Contig length of XXX genome", fontsize.labels = 0)
```



## treemapify

使用treemap绘图产生的图形不是ggplot对象，而`trrmapify`则解决了这一问题。

### 安装

```R
install.packages("treemapify")
```

## 使用

```R
library(ggplot2)
library(treemapify)
#使用treemapify自带的示例数据G20
ggplot(data = G20, 
       aes(area = gdp_mil_usd)  # 输入的映射中，只有area为必须
       ） +
       geom_treemap()
```

![img](https://pic2.zhimg.com/80/v2-acd5f391e31e3ceb8c0615deaa0fb865_hd.jpg)

### 调整

默认的输出为黑色背景，这一点可以手动赋值给`fill`映射修改

```R
ggplot(G20, aes(area = gdp_mil_usd)) + 
 geom_treemap(fill="white")
```

还可以向`fill`映射另一个变量

```R
ggplot(G20, aes(area = gdp_mil_usd, fill = hdi)) + 
 geom_treemap() +
 scale_fill_distiller(palette="Greens") #使用内置的配色方案
```

使用`geom_treemap_text`添加标签，不支出ggplot通用的`geom_text()`函数

```R
ggplot(G20, aes(area = gdp_mil_usd, fill = hdi, label = country)) +
 geom_treemap() +
 geom_treemap_text(fontface = "italic", colour = "red", place = "centre",grow = TRUE,alpha=.6)+
 scale_fill_distiller(palette="Greens")
```

## 参考来源

https://www.r-bloggers.com/simple-steps-to-create-treemap-in-r/

https://rdrr.io/cran/treemap/man/treemap.html

https://rpubs.com/brandonkopp/creating-a-treemap-in-r

https://cran.r-project.org/web/packages/treemap/treemap.pdf

https://github.com/wilkox/treemapify

https://zhuanlan.zhihu.com/p/29834340

<hr />