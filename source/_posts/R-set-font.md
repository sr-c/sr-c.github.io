---
title: ggplot的字体设置
tags: [R,ggplot,font]
date: 2019-04-19 22:32:30
permalink:
categories:
description:
---
<p class="description">ggplot的字体设置</p>

<!-- more -->

## geom_text

`geom_text`是直接向ggplot对象中添加绘制的文字

## theme

在`theme`中以`element_text()`设置字体

> element_text(family = NULL**,** face = NULL**,** colour = NULL**,** size = NULL**,** hjust = NULL**,** vjust = NULL**,** angle = NULL**,** lineheight = NULL)

其中，`title`设置图表的标题，坐标轴标题以及图例的文本样式。`axis.text`则对应坐标轴刻度标尺的字体。

## theme_set

使用`theme_set`函数可全局设置特定主题中的字体大小

```R
# 全局设置某个主题的默认字体大小
theme_set(theme_gray(base_size = 18))
qplot(1:10, 1:10)
# 或直接在主题中设置base_size
qplot(1:10, 1:10) + theme_grey(base_size = 18) 
ggplot(mtcars, aes(x = mpg, y = cyl)) + 
geom_point() +
theme_grey(base_size = 18) 
```

## yyplot::set_font

使用Y叔提供`yyplot`中提供的[`set_font`](https://guangchuangyu.github.io/cn/2017/09/ggplot2-set-font/)函数可以在最后同一设置文字。（对于`geom_text`, 或`theme`中的`axis.text`, `text`均生效）

## 参考来源

https://www.cnblogs.com/wkslearner/p/5701207.html

https://stackoverflow.com/questions/25061822/ggplot-geom-text-font-size-control

https://cloud.tencent.com/developer/ask/65053

https://guangchuangyu.github.io/cn/2017/09/ggplot2-set-font/

https://cran.r-project.org/web/packages/svglite/vignettes/fonts.html

<hr />