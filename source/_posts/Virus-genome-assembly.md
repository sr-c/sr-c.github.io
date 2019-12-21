---
title: Viral-genome-assembly
tags: [assembly]
date: 2019-10-26 21:57:04
permalink:
categories:
description:
---
<p class="description">使用多种工具基于Illumina数据组装病毒基因组</p>
<!-- more -->

目前一般的方法有[VICUNA](https://www.broadinstitute.org/viral-genomics/vicuna), [PRICE](http://derisilab.ucsf.edu/software/price/index.html), [IVA](https://sanger-pathogens.github.io/iva/), 因此，我们尝试使用这3种方法进行组装。

## 本地工具

### VICUNA

VICUNA出自大名鼎鼎的borad研究所，但在2012年后，就停止了开发。

### PRICE

DeRisi实验室出品，2014年停止开发。组装需提供一个seed sequence

### IVA

总结之前的工具，发表于Gigascience

## 网页工具

### VirAmp

默认使用`velvet`进行组装

> **VirAmp workflow**
>
> Virus Assembly Pipeline contains a set of programs to assemble the  the virus genome from shotgun sequence in a fast and accurate way with a related genome as guide.  In general, it contains the following steps:
>
> 1. Quality Control
>
> This step trims out the low quality bases in reads from both end using seqtk toolkit
>
> 2. Diginal Normalization
>
> Digital normalization (diginorm) uses a single-pass computational  algorithm to reduce the high and redundant coverage in shotgun sequence  dataset, thus significantly decreasing time and memory requirements for  the de novo assembly while retaining the accuracy.
>
> **Notice** Since Digital Normalization is designed to deal with extreme high  coverage data (x1000 or above), by default it's not turned on. One can  choose to turn on at the input panel.
>
> 3. De novo assembly (velvet)
>
> This step uses the velvet program to do a de novo assembly from the  shotgun sequence. This step results in a set of contigs of various  lengths.
>
> 4. Reference-guided Scaffolding (AMOScmp)
>
> Contigs generated from velvet in step 3 are oriented and connected into scaffolds by the reference-guided assembly tool AMOScmp
>
> 5. Scaffolding using paired-end information (SSPACE)
>
> Uses paired-end reads from the original dataset to extend and scaffold contigs
>
> 6. Assembly assessment
>
> QUAST (Quality Assessment Tool for Genome Assemblies) program is used for the statistical assessment of the assemblies resulting from step 4
>
> 7. SNV generation
>
> MUMmer is used to align the assembly results to the reference genome and report SNVs.
>
> 8. Draft and reference genome comparison
>
> MUMmer is used to compare the assembly results (draft genome) with the reference genome.

### VirusTAP

https://www.frontiersin.org/articles/10.3389/fmicb.2016.00032/full

需注册

## 参考来源

<hr />