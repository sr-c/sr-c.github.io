---
title: 使用alienness识别基因水平转移
tags: [HGT,alienness]
date: 2018-09-27 22:27:27
permalink:
categories:
description:
---
<p class="description">使用alienness识别基因水平转移</p>

<!-- more -->

## 官方描述

[alienness](http://alienness.sophia.inra.fr/cgi/index.cgi)需要输入蛋白序列比对到nr数据库的结果文件作为输入

其对blastp的需求如下

| -option value | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| -outfmt 7     | 7 = tabular with comment lines !MANDATORY                    |
| -db nr        | BLAST database name  For a better coverage of the biodiversity, NCBI's nr library is recommanded but not necessary.  The protein library must have gi or accession numbers that exist in the NCBI database. |
| -seg no       | The SEG program is used to mask or filter low complexity regions in amino acid queries |
| -evalue 1e-3  | Expect value (E) for saving hits                             |

## 记录

实际上，alienness需要结果文件第2列subject id中含有gi 或 accession numbers 使其识别subject id的物种来源。且其格式应当如下

```bash
gi|831779554|ref|XP_012755758.1|
```

但有时blast输出的结果第二列为subject acc.ver，其内容只是accession number的一部分

```bash
XP_018661062.1
```

如此，并不能得到我们需求的结果

## 参考来源

http://alienness.sophia.inra.fr/cgi/faq.cgi

<hr />