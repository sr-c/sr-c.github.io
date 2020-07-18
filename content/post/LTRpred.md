---
title: LTRpred
tags: [LTRpred]
date: 2018-12-22 14:52:20
permalink:
categories:
description:
---
<p class="description">使用LTRpred从头分析基因组中的LTR序列</p>

<!-- more -->

[LTRpred](https://github.com/HajkD/LTRpred)实际是调用`GenomeTools`中封装的`LTRharvest`对基因组序列进行*de novo* 预测，并使用`LTRdigest`开展分析，并包含了一些可视化的函数。此外，`LTRpred`还包括了`LTRpred.meta()`函数，方便调用多个基因组进行meta分析。

## 软件安装

### 环境依赖

```bash
#conda安装GenomeTools依赖2.7版本的python
conda create -n LTRpred python=2.7
source activate LTRpred
conda install hmmer genometools vsearch dfam
conda install -c bioconda blast
#Install USEARCH from git or direct download

```

### 程序安装

此时R的默认版本仍为3.4.3，仍然使用bioconductor 3.7的方法安装

```R
# install Bioconductor
## try https:// if http:// URLs are not supported
source("http://bioconductor.org/biocLite.R")
biocLite()
## install prerequisite Bioconductor packages
biocLite(c("GenomicFeatures","IRanges", "AnnotationDbi", "Biostrings"))
## install LTRpred as follows
source("http://bioconductor.org/biocLite.R")
biocLite("devtools")
biocLite("HajkD/LTRpred")
```

## Quick Start

`LTRpred`自带人Y染色体序列作为输入，实验一下程序的运行

```R
# load LTRpred package
library(LTRpred)
# de novo LTR transposon prediction for the Human Y chromosome
LTRpred(
    genome.file = system.file("Hsapiens_ChrY.fa", package = "LTRpred"),
    cluster     = TRUE,
    cores = 4
)
```

## 程序输出

`LTRperd()`会将结果输出在`*_ltrpred`文件夹中

## 参考来源

https://hajkd.github.io/LTRpred/

https://hajkd.github.io/LTRpred/articles/Introduction.html

<hr />