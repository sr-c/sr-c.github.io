---
title: 简易计算RNA-seq差异表达基因
tags: [nf-core, RNA-seq, Trinity]
date: 2020-05-25 20:50:18
permalink:
categories:
description:
---
<p class="description">使用Trinity提供的分析脚本筛选差异基因。</p>

<!-- more -->

使用`nf-core`，能够很方便地进行RNA-seq的上游分析，得到表达矩阵。但是，大多数时候，我们更关心的是筛选差异表达基因。别担心，`Trinity`提供了一些简便的分析脚本供我们使用。

## 整理上游数据

### 表达矩阵

在`nf-core`的结果目录中，`featureCounts`目录下的`merged_gene_counts.txt`为相应工具计算得到的表达矩阵。

> 如果在`nf-core`流程中还通过`--pseudo_aligner salmon`参数引入了`salmon`作为比对工具的话。在结果目录的`salmon`路径下的`salmon_merged_gene_counts.csv`为基因水平的表达矩阵，`salmon_merged_transcript_counts.csv`为转录本水平的表达矩阵。

经过简单的文本处理后，可以得到供后续分析的表达矩阵。

```bash
cat merged_gene_counts.txt | \
  cut -f 1,3-23 | \
  sed 's/_1.cleanAligned.sortedByCoord.out.bam//g' | \
  sed 's/Geneid//' > featureCounts_merged_gene_counts.tab
```

`featureCounts_merged_gene_counts.tab`的内容形式如下

```bash
        GroupD3   GroupA5   GroupC1   GroupC2   GroupA2   GroupA1   GroupC5   GroupB6   GroupB1   GroupD2   GroupC3   GroupA3   GroupA6   GroupA4   GroupC4   GroupB5   GroupD1   GroupB2   GroupB4
IOZ07G0001      6005    8108    5144    6331    6539    10535   4612    7234    5743    5152    9328    5992    6663    9983    3619    7062    5809    8536
IOZ07G0002      2970    3090    3035    3306    1924    2709    3816    4076    4293    2196    4437    1816    1862    3397    3354    2791    2657    4181
IOZ07G0003      1380    2109    2057    2247    1966    2718    1941    1969    2012    1526    1860    1682    1735    2338    1899    1408    1613    1942
IOZ07G0004      6778    4952    9068    7378    4473    6409    6472    5752    6326    6779    6370    3759    3337    6875    5866    5574    6853    6540

```

### 分组信息

如果分组不太复杂的话，可手动编辑一个以换行符分隔的分组矩阵`samples.txt`，形式如下

```bash
GroupA     GroupA1
GroupA     GroupA2
GroupA     GroupA3
GroupA     GroupA4
GroupA     GroupA5
GroupA     GroupA6
GroupB     GroupB1
GroupB     GroupB2
GroupB     GroupB3
GroupB     GroupB4
GroupB     GroupB5
GroupB     GroupB6
GroupC     GroupC1
GroupC     GroupC2
GroupC     GroupC3
GroupC     GroupC4
GroupC     GroupC5
GroupC     GroupC6
GroupD     GroupD1
GroupD     GroupD2
GroupD     GroupD3
```

## 简便差异分析

通过`Trinity`提供的脚本`Analysis/DifferentialExpression/run_DE_analysis.pl`可快速地调用`DESeq2`或`voom`进行差异表达分析。因此，运行前提要求当前的`R`环境中已安装好相应的软件包。

```bash
matrix=[featureCounts_merged_gene_counts.tab, salmon_merged_gene_counts.tab]
method=[DESeq2, voom]
~/software/trinityrnaseq-Trinity-v2.8.5/Analysis/DifferentialExpression/run_DE_analysis.pl \
  --matrix featureCounts_merged_gene_counts.tab \
  --samples_file samples.txt \
  --method DESeq2 \
  --output DESeq2.featureCounts.Organism.dir
```

通过`--output`指定输出路径，即可得到分析结果。

一般来说，还需要通过FDR < 0.05 和 log2|FC| > 1的阈值筛选得到目标差异基因。

## 参考来源

https://bioconductor.org/packages/3.7/bioc/vignettes/DESeq2/inst/doc/DESeq2.html

https://abego.cn/2019/05/31/deseq-analysis-the-different-expression-gene/

http://xuchunhui.top/2020/03/28/%E4%BD%BF%E7%94%A8DEseq2%E5%88%86%E6%9E%90RNA-seq%E6%95%B0%E6%8D%AE/

<hr />