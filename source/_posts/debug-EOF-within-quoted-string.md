---
title: 【debug】EOF within quoted string
tags: [debug,R]
date: 2019-03-31 22:26:54
permalink:
categories:
description:
---
<p class="description">使用R读入数据时发生怪异错误</p>

<!-- more -->

## 问题描述

在R中读入外部数据时，得到warning

```R
Warning message:
In scan(file, what, nmax, sep, dec, quote, skip, nlines, na.strings,  :
  EOF within quoted string
```

这可能是由于数据表格中存在引号`"`或`'`, 而默认设置读入数据的`read.table()`函数的`quote ="\"'" `

## 解决方案

在读入数据时，通过`quote = ""`禁用引用

```R
cit <- read.csv("citations.CSV", quote = "", 
                 row.names = NULL, 
                 stringsAsFactors = FALSE)
```

## 参考来源

https://codeday.me/bug/20170630/34178.html

https://www.rdocumentation.org/packages/utils/versions/3.5.3/topics/read.table

<hr />