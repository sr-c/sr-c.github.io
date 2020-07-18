---
title: 计算基因组中LTR的插入时间
tags: [LTR,LTRharvest,LTR-retriever]
date: 2018-12-22 14:52:20
permalink:
categories:
description:
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
<p class="description">使用LTR预测工具分析基因组中的LTR序列，并计算其插入时间</p>
<!-- more -->

# Aging LTR insertion

使用`LTRharvest`与`c`计算LTR-RTs在基因组中的插入时间。
## LTRharvest
`LTRharvest`是`Genome tools`中进行*de novo*识别LTR的组件。不依赖于序列相似性，根据LTR-RT的结构特点进行识别。

```bash
#LTRharvest并不会根据输入的序列名进行命名，而根据序列在fasta文件中的顺序在输出中命名为seq0，seq1...
#因此可手动将输入文件中的scaffold整合为一条，方面后续根据gff文件输出LTR序列

gt suffix -db <path-to-your-genome> -index <genome-index>
gt ltrharvest -index <genome-index> -motif TGCA -motifmis 1 -gff3 out/genome.harvest.gff -out out/genome.harvest.out > genome.harvest.scn
gt ltrharvest -index <genome-index> -gff3 out/genome.harvest.nonTGCA.gff -out out/genome.harvest.nonTGCA.out > genome.harvest.nonTGCA.scn
```
得到输出的结果中往往存在着数千条LTR-RTs，但其中存在着许多的假阳性。距离相近的其他类型重复序列，若其相似性较高，往往也会被识别为LTR-RT。LTR-retriever能够过滤这一结果，降低假阳性，得到完整的LTR-RT。
## LTR-retriever
`LTR-retriever`能够识别`LTRharvest`, `LTR_Finder`或`MGEScan_LTR`的输出，对结果进行过滤，得到完整的LTR-RT。
LTR-retriever基于的假设是，LTR-RT在新插入基因组序列时，两端的LTR序列是相同的，同时，LTR序列两端的序列是不同的，以此来判断LTR序列的边界。
`LTR-retriever`计算LTR序列间的距离，基于物种的突变速率（默认使用水稻的）计算LTR的插入时间。基于`bin/LTR.indefinder.pl`使用JK法计算。
### 计算DNA序列间的距离
最简单的算法是Jukes-Cantor 模型，默认ACGT四种碱基之间的替换速率相同。
$$
d_{AB}=-\frac{3}{4}\ln\left( 1-\frac{4}{3}f_{AB}\right)
$$
>where f<sub>AB</sub> is the **dissimilarity (fraction of observed differences)** between sequences A and B, and d<sub>AB</sub> is the **estimated evolutionary distance (fraction of expected substitutions)** between sequences A and B. 

实际上，由于嘧啶与嘌呤之间的替换比同种碱基之间的替换更为更为困难，将transitions与transversions分开考虑，则使用的是Kimura 2-parameter 模型
$$
d_{AB}=-\frac{1}{2}\ln \left [ \left ( 1-2P-Q \right )\sqrt{1-2Q} \right ]
$$

>**where P is the fraction of sequence positions differing by a transition and Q is the fraction of sequence positions differing by a transversion.**

### 计算LTR插入时间

基于`LTR-RT`两端`5'LTR`与`3'LTR`序列之间的距离`K`，我们可以计算该LTR-RT在插入基因组后所经历的时间`T`。
$$
K=-\frac{1}{2}\ln \left [ \left ( 1-2P-Q \right )\sqrt{1-2Q} \right ]
$$


其中，`P`为transitions的占比，`Q`为transversions的占比。

基于\\(T=\frac{K}{2\mu} \\)我们可以很容易地由`K`值计算得到`T`，其中`μ`为目标物种的自然变异速率（水稻的变异速率为1.3e-8 bp<sup>-1</sup>·year<sup>-1</sup> ）。

# 参考来源

https://github.com/SIWLab/Lab_Info/wiki/Ageing-LTR-insertions

http://bioinformatics.psb.ugent.be/downloads/psb/Userman/treecon_distance.html