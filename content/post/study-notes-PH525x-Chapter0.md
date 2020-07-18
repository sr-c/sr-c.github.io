---
title: PH525x学习笔记Ⅰ
tags: [PH525x,笔记]
date: 2018-09-06 19:48:10
permalink:
categories:
description:
---
<p class="description">PH525x: Data Analysis for Genomics是一门哈佛大学的基因组学数据分析课程</p>

<!-- more -->

## 数据导入

### 安装软件包

```R
install.packages("rafalib")
install.packages("downloader")
```

下载课程[地址](https://github.com/genomicsclass/dagdata/tree/master/inst/extdata)中的`femaleMiceWeights.csv`，并导入数据到R中。

### 方法一

直接下载文件到工作目录下，然后使用read.csv导入数据

```R
dat <- read.csv("femaleMiceWeights.csv")
```

### 方法二

```R
library(downloader) ##use install.packages to install
url <- "https://raw.githubusercontent.com/genomicsclass/dagdata/master/inst/extdata/femaleMiceWeights.csv"
filename <- "femaleMiceWeights.csv" 
download(url, destfile=filename)

dat <- read.csv(filename)
```

## 数据清理`dplyr`

读入`femaleMiceWeights.csv`的数据后，筛选我们想要的数据

```R
library(dplyr) 
chow <- filter(dat, Diet=="chow") #keep only the ones with chow diet
head(chow)

chowVals <- select(chow,Bodyweight) #选择chow中Bodyweight这一列
```

在`dplyr`中可以使用管道符号`%>%`来构建连续任务，上述步骤可以在一步完成

```R
chowVals <- filter(dat, Diet=="chow") %>% select(Bodyweight)
```

上述命令不会改变数据的性质，若想要将列表转变为向量，则应使用`unlist`函数

```R
chowVals <- filter(dat, Diet=="chow") %>% select(Bodyweight) %>% unlist
```

<details>
	<summary>作业——统计灵长类动物的睡眠时间，并计算均值</summary>
	<p>mean_sleep_total<-filter(dat,order=="Primates") %>% select(sleep_total) %>% unlist %>% mean</p>
</details>

## summarize函数

该函数的作用是将多个数值降维至单一数值，用法如下

summarise(.data, ...)

>Useful functions
>
>Center: mean(), median()
>
>Spread: sd(), IQR(), mad()
>
>Range: min(), max(), quantile()
>
>Position: first(), last(), nth(),
>
>Count: n(), n_distinct()
>
>Logical: any(), all()

```R
dat %>% summarise(mean = mean(sleep_total)) %>% unlist
```

## 数学符号

编号，求和，希腊字母

### 希腊字母

***μ*** **, the Greek letter for m (m is for mean)**

***σ*** **, the Greek letter for s(s is for standard deviation )**

***ε*** **, the Greek letter for e(e is for measurement error )**

***β*** **, Effect sizes**

## ∞

>In the text we often talk about *asymptotic* results. Typically, 
>this refers to an approximation that gets better and better as the 
>number of data points we consider gets larger and larger, with perfect 
>approximations occurring when the number of data points is ∞. In practice, there is no such thing as ∞,
>but it is a convenient concept to understand. One way to think about 
>asymptotic results is as results that become better and better as some 
>number increases and we can pick a number so that a computer can’t tell 
>the difference between the approximation and the real number. Here is a 
>very simple example that approximates 1/3 with decimals:

```R
onethird <- function(n) sum( 3/10^c(1:n))
1/3 - onethird(4)
## [1] 3.333333e-05

1/3 - onethird(10)
## [1] 3.333334e-11

1/3 - onethird(16)
## [1] 0
```

此时，16即是无穷大

## 积分

∫42f(x)dx

```R
width <- 0.01
x <- seq(2,4,width)
areaofbars <-  f(x)*width
sum( areaofbars )

## [1] 0.02298998
```

## 参考来源

http://genomicsclass.github.io/book/pages/dplyr_intro.html

http://genomicsclass.github.io/book/pages/dplyr_intro_exercises.html

http://genomicsclass.github.io/book/pages/math_notation.html

<hr />