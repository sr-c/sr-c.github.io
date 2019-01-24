---
title: 【R】转换数据格式
tags: [R,tidyr,data format]
date: 2018-10-11 22:35:51
permalink:
categories:
description:
---
<p class="description">将数据由wide format转换为long format</p>

<!-- more -->

## 数据格式

Wide format为每列一个变量，也是常见的SPSS，excel等存储数据的格式。例如在基因表达数据中，每行一个基因，每列一个样品，构成一个数据矩阵，具有较强的人类可读性。

Long format的最简格式为两列，第一列为变量名，第二列为数值。基因表达矩阵转换为long format后，由三列构成。第一列为基因ID，第二列为样品名，第三列则为基因表达量。此格式更方便用于数据分析。

- wide format

| 基因  | 分生组织 | 根   | 花   |
| ----- | -------- | ---- | ---- |
| gene1 | 582      | 91   | 495  |
| gene2 | 305      | 3505 | 33   |

- long format

| 基因  | 组织     | 表达量 |
| ----- | -------- | ------ |
| gene1 | 分生组织 | 582    |
| gene2 | 分生组织 | 305    |
| gene1 | 根       | 91     |
| gene2 | 根       | 3503   |
| gene1 | 花       | 492    |
| gene2 | 花       | 33     |

大多数情况下，人类的实验原始记录常以wide format形式存储，而进行数据分析时，需要将其转换为更易操作的long format形式。

## 解决方案

R的`tidyr`包就是数据清理的好帮手。`tidyr`使用`gather()`与`spread()`函数来改变数据格式。

```R
olddata_wide <- read.table(header=TRUE, text='
 subject sex control cond1 cond2
       1   M     7.9  12.3  10.7
       2   F     6.3  10.6  11.1
       3   F     9.5  13.1  13.8
       4   M    11.5  13.4  12.9
')
# Make sure the subject column is a factor
olddata_wide$subject <- factor(olddata_wide$subject)
```

### From wide to long

Use `gather`:

```R
olddata_wide
#>   subject sex control cond1 cond2
#> 1       1   M     7.9  12.3  10.7
#> 2       2   F     6.3  10.6  11.1
#> 3       3   F     9.5  13.1  13.8
#> 4       4   M    11.5  13.4  12.9

library(tidyr)

# The arguments to gather():
# - data: Data object
# - key: Name of new key column (made from names of data columns)
# - value: Name of new value column
# - ...: Names of source columns that contain values
# - factor_key: Treat the new key column as a factor (instead of character vector)
data_long <- gather(olddata_wide, condition, measurement, control:cond2, factor_key=TRUE)
data_long
#>    subject sex condition measurement
#> 1        1   M   control         7.9
#> 2        2   F   control         6.3
#> 3        3   F   control         9.5
#> 4        4   M   control        11.5
#> 5        1   M     cond1        12.3
#> 6        2   F     cond1        10.6
#> 7        3   F     cond1        13.1
#> 8        4   M     cond1        13.4
#> 9        1   M     cond2        10.7
#> 10       2   F     cond2        11.1
#> 11       3   F     cond2        13.8
#> 12       4   M     cond2        12.9
```

## 参考来源

http://www.cookbook-r.com/Manipulating_data/Converting_data_between_wide_and_long_format/

https://www.jianshu.com/p/e3eaea18650d

<hr />