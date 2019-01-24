---
title: makeblastdb-fails
tags: [debug,blast]
date: 2018-09-25 22:33:45
permalink:
categories:
description:
---
<p class="description">在对某些特殊序列名的数据库建库时出现报错，记录如下</p>

<!-- more -->

## 问题描述

对某个特定下载的数据库使用`makeblastdb`建库时，出现`No volumes were created because no sequences were found.`报错

## 使用环境

BLAST 2.2.28+

需要建库的fasta文件内容格式如下

```
>gnl|TC-DB|1001796365|4.F.1.1.5 CDP-alcohol phosphatidyltransferase [Marinobacter excellens]
xxxxxxxxxxxxxxxxxxxx
>gnl|TC-DB|YP_009506345.1|9.B.308.4.1 p4 [Cordyline virus 1]
xxxxxxxxxxxxxxxxxxxx
```

## 解决方案

删除序列名中重复的部分，删去`gnl|`即可运行`makeblastdb`

似乎此bug在BLAST 2.2.30+版本后被修正，目前还未验证。

## 参考来源

https://github.com/wurmlab/sequenceserver/issues/74

https://www.biostars.org/p/115984/

<hr />