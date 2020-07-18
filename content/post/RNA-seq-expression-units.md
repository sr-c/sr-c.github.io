---
title: RNA-seq的表达量计算单元
tags: [RNA-seq]
date: 2019-05-27 23:08:52
permalink:
categories:
description:
---
<p class="description">对转录组数据的定量</p>

<!-- more -->

定量过程可以分为三个水平：基因水平(gene-level), 转录本水平(transcript-level), 外显子水平(exon-usage-level).

## 标准化过程

考虑`测序深度`与`转录本长度`对表达量的影响

### TPM

Transcripts per million (TPM) 是对RNA-seq的推荐标准化方法。

![\text{TPM}_i = \dfrac{X_i}{\widetilde{l}_i} \cdot \left( \dfrac{1}{\sum_j \dfrac{X_j}{\widetilde{l}_j}} \right) \cdot 10^6](https://s0.wp.com/latex.php?latex=%5Ctext%7BTPM%7D_i+%3D+%5Cdfrac%7BX_i%7D%7B%5Cwidetilde%7Bl%7D_i%7D+%5Ccdot+%5Cleft%28+%5Cdfrac%7B1%7D%7B%5Csum_j+%5Cdfrac%7BX_j%7D%7B%5Cwidetilde%7Bl%7D_j%7D%7D+%5Cright%29+%5Ccdot+10%5E6&bg=ffffff&fg=000000&s=0)

### RPKM/FPKM

由counts直接除以测序深度与转录本长度，计算得到

![\text{FPKM}_i = \dfrac{X_i}{ \left(\dfrac{\widetilde{l}_i}{10^3}\right) \left( \dfrac{N}{10^6} \right)} = \dfrac{X_i}{\widetilde{l}_i N} \cdot 10^9  ](https://s0.wp.com/latex.php?latex=%5Ctext%7BFPKM%7D_i+%3D+%5Cdfrac%7BX_i%7D%7B+%5Cleft%28%5Cdfrac%7B%5Cwidetilde%7Bl%7D_i%7D%7B10%5E3%7D%5Cright%29+%5Cleft%28+%5Cdfrac%7BN%7D%7B10%5E6%7D+%5Cright%29%7D+%3D+%5Cdfrac%7BX_i%7D%7B%5Cwidetilde%7Bl%7D_i+N%7D+%5Ccdot+10%5E9++&bg=ffffff&fg=000000&s=0)

### FPKM to TPM

TPM的计算公式可变形为

![\begin{aligned}  \text{TPM}_i &= \dfrac{X_i}{\widetilde{l}_i} \cdot \left( \dfrac{1}{\sum_j \dfrac{X_j}{\widetilde{l}_j}} \right) \cdot 10^6 \\  &\propto \dfrac{X_i}{\widetilde{l}_i \cdot N} \cdot \left( \dfrac{1}{\sum_j \dfrac{X_j}{\widetilde{l}_j \cdot N} } \right) \\  &\propto \dfrac{X_i}{\widetilde{l}_i \cdot N} \cdot 10^9   \end{aligned}  ](https://s0.wp.com/latex.php?latex=%5Cbegin%7Baligned%7D++%5Ctext%7BTPM%7D_i+%26%3D+%5Cdfrac%7BX_i%7D%7B%5Cwidetilde%7Bl%7D_i%7D+%5Ccdot+%5Cleft%28+%5Cdfrac%7B1%7D%7B%5Csum_j+%5Cdfrac%7BX_j%7D%7B%5Cwidetilde%7Bl%7D_j%7D%7D+%5Cright%29+%5Ccdot+10%5E6+%5C%5C++%26%5Cpropto+%5Cdfrac%7BX_i%7D%7B%5Cwidetilde%7Bl%7D_i+%5Ccdot+N%7D+%5Ccdot+%5Cleft%28+%5Cdfrac%7B1%7D%7B%5Csum_j+%5Cdfrac%7BX_j%7D%7B%5Cwidetilde%7Bl%7D_j+%5Ccdot+N%7D+%7D+%5Cright%29+%5C%5C++%26%5Cpropto+%5Cdfrac%7BX_i%7D%7B%5Cwidetilde%7Bl%7D_i+%5Ccdot+N%7D+%5Ccdot+10%5E9+++%5Cend%7Baligned%7D++&bg=ffffff&fg=000000&s=0)

因此，可以由FPKM转化得到TPM

![\begin{aligned}  \text{TPM}_i &= \left( \dfrac{\text{FPKM}_i}{\sum_j \text{FPKM}_j } \right) \cdot 10^6  \end{aligned}  ](https://s0.wp.com/latex.php?latex=%5Cbegin%7Baligned%7D++%5Ctext%7BTPM%7D_i+%26%3D+%5Cleft%28+%5Cdfrac%7B%5Ctext%7BFPKM%7D_i%7D%7B%5Csum_j+%5Ctext%7BFPKM%7D_j+%7D+%5Cright%29+%5Ccdot+10%5E6++%5Cend%7Baligned%7D++&bg=ffffff&fg=000000&s=0)

## R code

```R
countToTpm <- function(counts, effLen)
{
    rate <- log(counts) - log(effLen)
    denom <- log(sum(exp(rate)))
    exp(rate - denom + log(1e6))
}
 
countToFpkm <- function(counts, effLen)
{
    N <- sum(counts)
    exp( log(counts) + log(1e9) - log(effLen) - log(N) )
}
 
fpkmToTpm <- function(fpkm)
{
    exp(log(fpkm) - log(sum(fpkm)) + log(1e6))
}
 
countToEffCounts <- function(counts, len, effLen)
{
    counts * (len / effLen)
}

################################################################################
# An example
################################################################################
cnts <- c(4250, 3300, 200, 1750, 50, 0)
lens <- c(900, 1020, 2000, 770, 3000, 1777)
countDf <- data.frame(count = cnts, length = lens)
 
# assume a mean(FLD) = 203.7
countDf$effLength <- countDf$length - 203.7 + 1
countDf$tpm <- with(countDf, countToTpm(count, effLength))
countDf$fpkm <- with(countDf, countToFpkm(count, effLength))
with(countDf, all.equal(tpm, fpkmToTpm(fpkm)))
countDf$effCounts <- with(countDf, countToEffCounts(count, length, effLength))
```

使用`apply`函数计算TPM

```R
tpms <- apply(expMatrix, 2, fpkmToTpm)
tpms[1:3,]
```

检查每列的总和是否一致

```R
colSums(tpms)
```



## 参考来源

https://haroldpimentel.wordpress.com/2014/05/08/what-the-fpkm-a-review-rna-seq-expression-units/

https://www.jianshu.com/p/9dfb65e405e8

https://zhuanlan.zhihu.com/p/26750663

http://www.bioinfo-scrounger.com/archives/342

<hr />