---
title: 【debug】C stack usage is too close to the limit
tags: [debug, R]
date: 2019-02-16 21:54:19
permalink:
categories:
description:
---
<p class="description">运行Rscript读取数据时提示报错</p>

<!-- more -->

## 问题描述

```bash
Error: C stack usage  9964644 is too close to the limit
```

这似乎是由于数据过大导致

## 解决方案

```bash
$ ulimit -s # print default
10240
$ R --slave -e 'Cstack_info()["size"]'
   size
9961472
```

这个值本应该等于10240*1024=10485760，但我们得到的9961472比这略小一些，在此不明原因。

解决方案是手动设定C stack

```bash
ulimit -s 102400
```

然后重新运行程序，即可解决

## 参考来源

https://stackoverflow.com/questions/14719349/error-c-stack-usage-is-too-close-to-the-limit

http://blog.sina.com.cn/s/blog_86c8fc120102w442.html

<hr />