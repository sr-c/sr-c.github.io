---
title: 【R】饼图
tags: [R]
date: 2019-01-02 19:30:42
permalink:
categories:
description:
---
<p class="description">虽然StatQuest中推荐我们能使用柱形图就不要使用扇形图，但是试试又何妨呢</p>

<!-- more -->

使用基本的`pie()`函数能够直接画出饼状图

```R
pie(x, labels, radius, main, col, clockwise)
```

## 简单方案

```R
# Create data for the graph.
x <- c(11, 30, 39, 20)
labels <- c("70后", "80后", "90后", "00后")

# Give the chart file a name.
png(file = "birth_of_age.jpg")

# Plot the chart.
pie(x,labels)

# Save the file.
dev.off()
```

## ggplot2实现

```R
library(ggplot2) 
dt = data.frame(A = c(2, 7, 4, 10, 1), B = c('B','A','C','D','E')) 
dt = dt[order(dt$A, decreasing = TRUE),] myLabel = as.vector(dt$B) myLabel = paste(myLabel, "(", round(dt$A / sum(dt$A) * 100, 2), "%)", sep = "") 
p = ggplot(dt, aes(x = "", y = A, fill = B)) + geom_bar(stat = "identity", width = 1) + coord_polar(theta = "y") + labs(x = "", y = "", title = "") + theme(axis.ticks = element_blank()) + theme(legend.title = element_blank(), legend.position = "top") + scale_fill_discrete(breaks = dt$B, labels = myLabel) + theme(axis.text.x = element_blank()) + geom_text(aes(y = A/2 + c(0, cumsum(A)[-length(A)]), x = sum(A)/20, label = myLabel), size = 5) ## 在图中加上百分比：x 调节标签到圆心的距离, y 调节标签的左右位置 
p
```

## 待解决

如何将需重点突出的扇形区块位移，做出如下的形式

![preview](https://pic3.zhimg.com/v2-75442faaf563a0ca7149b175fa0317be_r.jpg)

## 参考来源

https://www.yiibai.com/r/r_pie_charts.html

https://blog.csdn.net/Bone_ACE/article/details/47455363

https://zhuanlan.zhihu.com/p/26745812

<hr />