---
title: RNA-seq-123
tags: [RNAS-seq]
date: 2019-06-20 21:21:34
permalink:
categories:
description:
---
<p class="description">RNA-seq下游分析</p>
<!-- more -->

## 初始配置

```R
library(limma)
library(Glimma)
library(edgeR)
library(Mus.musculus)
```

## 读入数据

```R
#使用DGEList读取counts矩阵
genelist <- DGEList(counts = salmon_counts[2:31], group = group)
#使用tximport读入数据
## 读入分组信息

##读入基因注释信息
genelist$genes
```

## 过滤低表达基因

推荐使用`edgeR`提供的`filterByExpr`函数进行过滤

```R
#直接根据所有样品中的表达量进行过滤
table(rowSums(genelist$counts==0)==30)

#使用
keep.exprs <- filterByExpr(genelist, group = group)
genelist <- genelist[keep.exprs,, keep.lib.sizes=FALSE]
dim(genelist)
```

## 标准化

```R
x <- calcNormFactors(genelist, method = "TMM")

x$genes <- x$genes[rownames(x$counts),]
```

## 差异表达分析

### limma-voom

```R
# 输入设计矩阵
design <- model.matrix(~0+group)
colnames(design) <- levels(group)
logcpm <- cpm(x, log = TRUE, prior.count = 2)

contr.matrix <- makeContrasts(
  IOZ07.vs.1621 = Mature - Mature1621,
  Mature.vs.vitro = Mature - Invitro,
  vitro.vs.1621 = Proliferation - Mature,
  Mature.vs.Transition = Mature - Transition,
  Transition.vs.Mycelium = Transition - Mycelium,
  levels = colnames(design)
)
v <- voom(x, design, plot = TRUE)
vfit <- lmFit(v, design)
vfit <- contrasts.fit(vfit, contrasts = contr.matrix)
efit <- eBayes(vfit)
plotSA(efit, main="Final model: Mean-variance trend")
summary(decideTests(efit))
```




## 参考来源
https://bioconductor.org/packages/release/workflows/vignettes/RNAseq123/inst/doc/limmaWorkflow_CHN.html
https://github.com/COMBINE-lab/continuous_analysis_rnaseq
https://bioconductor.org/packages/devel/bioc/vignettes/tximport/inst/doc/tximport.html#limma-voom

<hr />