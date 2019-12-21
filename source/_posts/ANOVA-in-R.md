---
title: ANOVA-in-R
tags: [R,ANOVA]
date: 2019-11-15 20:47:57
permalink:
categories:
description:
---
<p class="description">单因素方差分析是分析多组数据均值的通用方法</p>

<!-- more -->

## 安装

```R
#安装一个田间实验设计方面的包，为我们提供LSD.test()等好用的函数
install.packages("agricolae")
library(agricolae)
##读入数据
data(sweetpotato)
```

安装过程可能会报错，需要我们手动安装`udunits`，`gdal`等依赖。

## 方差齐性检验

```R
oneway.test()
```



## 方差分析

```R
model <- aov(yield~virus, data = sweetpotato)
```



## 均值的多重比较

### LSD法

```R
#多重比较，不矫正P值
out <- LSD.test(model, "virus", p.adj = "none")
##标记字母法显示比较结果
out$group
#可视化
plot(out)
```



## 参考来源

http://vlambda.com/wz_x45GtwxNzx.html

https://data-flair.training/blogs/anova-in-r/

https://www.jianshu.com/p/553cc5987f61

<hr />