---
title: 将factor转为numeric
tags: [R,factor]
date: 2019-02-15 22:18:52
permalink:
categories:
description:
---
<p class="description">将factor转变为numetric</p>

<!-- more -->

## 预防方法

在数据处理过程中，时常会自动将字符串转变为因子，这对于后续的操作可能会带来困难。为避免此类麻烦，可以在程序中声明禁止chr转为factor

```R
options(stringsAsFactors = FALSE) #禁止chr转成factor
```

## 解决方案

若已经出现了这样的问题，或数据就是如此，我们应该如何处理呢

若直接使用`as.numeric()`函数则会将factor的不同level转换为对相应的数值，举例如下

```R
> directions <- c("North", "East", "South", "South")
> directions.factor <- factor(directions)
> directions.factor
[1] North East South South
Levels: East North South
# 直接转换会得到factor levels对应的numeric codes
> as.numeric(directions.factor)
[1] 2 1 3 3
```

若我们的`factor`本身就是数字，直接使用`as.numeric()`转换，则会丢失其原本的数值信息，而这往往不是我们想要的结果。因此，我们可以使用`varhandle`提供的`unfactor()`函数来方便地转换。

```R
# install.package("varhandle")
libarary(varhandle)
unfactor(your_factor_variable)
```

## 参考来源

https://stackoverflow.com/questions/3418128/how-to-convert-a-factor-to-integer-numeric-without-loss-of-information

https://www.dummies.com/programming/r/how-to-convert-a-factor-in-r/

<hr />