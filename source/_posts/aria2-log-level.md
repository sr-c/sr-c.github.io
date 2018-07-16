---
title: 设置aria2日志级别
tags: [aria2]
date: 2018-07-10 12:51:56
permalink:
categories:
description:
---
<p class="description">Aria2的log文件若无特殊设置，则体积增长非常快速。</p>

<!-- more -->

## 设置日志文件级别

添加`log-level`项目

```bash
log-level=warn
OR
log-level=error
```

## 删去设置中的log项

去除`aria2.conf`文件中的log项，程序运行时即不记录日志文件。

## 参考来源

https://www.jianshu.com/p/6adf79d29add

http://www.hostloc.com/thread-446920-1-1.html

<hr />