---
title: RPKM-normilization
tags: [RNA-seq, FPKM]
date: 2020-05-26 10:57:43
permalink:
categories:
description:
---
<p class="description">根据RNA-seq数据的表达矩阵，计算RPKM/FPKM</p>

<!-- more -->

> 现已不推荐使用RPKM/FPKM对RNA-seq数据进行定量比较，详情可[查看](http://mp.weixin.qq.com/s?__biz=MzIyNzgyNDAxMg==&mid=2247483918&idx=1&sn=947c04ff91546fdf5e76addc33644549&chksm=e85a1aeddf2d93fb8b09f925f086027cbdc007feefd6bbeafe13b0ccfdb51ab599b9c162f31e&scene=21#wechat_redirect)

## 计算原理

$$
RPKM = (10^9 * C) / (N * L)
$$

C = Number of reads mapped to a gene

N = Total mapped reads in the experiment

L = gene length in base-pairs for a gene

## 使用方法

```bash
perl rpkm_script_beta.pl sample_count_test.count 2:9 28 > sample_count_test.rpkm
```

其中，`sample_count_test.count`为用于计算的count矩阵，其中第2-9列为需要计算的样品的位置(C)，第28列为每个基因的有效长度(L)。

## 参考来源

https://github.com/decodebiology/rpkm_rnaseq_count

https://www.bioinfo-scrounger.com/archives/342/

<hr />