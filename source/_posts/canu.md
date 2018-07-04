---
title: canu的使用
tags: [canu]
date: 2018-07-04 13:05:17
permalink:
categories:
description:
---
<p class="description">canu的使用指南</p>

<!-- more -->

## 下载测试数据

```bash
curl -L -o pacbio.fastq http://gembox.cbcb.umd.edu/mhap/raw/ecoli_p6_25x.filtered.fastq
curl -L -o oxford.fasta http://nanopore.s3.climb.ac.uk/MAP006-PCR-1_2D_pass.fasta
```

## 一步执行

```bash
canu \
 -p ecoli -d ecoli-pacbio \
 genomeSize=4.8m \
 -pacbio-raw pacbio.fastq
```

## canu可分步骤执行

### Correct

```bash
canu -correct \
  -p ecoli -d ecoli \
  genomeSize=4.8m \
  -pacbio-raw  pacbio.fastq
```

### Trim

```bash
canu -trim \
  -p ecoli -d ecoli \
  genomeSize=4.8m \
  -pacbio-corrected ecoli/ecoli.correctedReads.fasta.gz
```

### Assemble

```bash
canu -assemble \
  -p ecoli -d ecoli-erate-0.039 \
  genomeSize=4.8m \
  correctedErrorRate=0.039 \
  -pacbio-corrected ecoli/ecoli.trimmedReads.fasta.gz

canu -assemble \
  -p ecoli -d ecoli-erate-0.075 \
  genomeSize=4.8m \
  correctedErrorRate=0.075 \
  -pacbio-corrected ecoli/ecoli.trimmedReads.fasta.gz
```

## 应补足的参数

```bash
useGrid=false \

gnuplotImageFormat=canvas \ or
gnuplotTested=true ##跳过gnuplot检测，在运行过程中不再使用gnuplot生成图片

## 如不添加上方gnuplot参数，则容易在程序启动前自检中出现如下报错
##（此问题似乎在1.7.1版本修复）
##ERROR:  Failed to detect a suitable output format for gnuplot.
##ERROR:  Looked for png, svg and gif, found none of them.

## java SE 8 
```

## Consensus Accuracy

Canu consensus sequences are typically well above 99% identity for  PacBio datasets.  Nanopore accuracy varies depending on pore and  basecaller version, but is typically above 98% for recent data. Accuracy  can be improved by polishing the contigs with tools developed specifically for that task.   We recommend [Quiver](http://github.com/PacificBiosciences/GenomicConsensus) for PacBio and [Nanopolish](http://github.com/jts/nanopolish) for Oxford Nanpore data. When Illumina reads are available, [Pilon](http://www.broadinstitute.org/software/pilon/) can be used to polish either PacBio or Oxford Nanopore assemblies.



For high coverage:

> - For more than 60X coverage, decrease the allowed difference in overlaps (from 4.5% to 4.0% with `correctedErrorRate=0.040` for PacBio, from 14.4% to 12% with `correctedErrorRate=0.12` for Nanopore), so that only the better corrected reads are used. This is primarily an optimization for speed and generally does not change assembly continuity.

## 参考来源

http://canu.readthedocs.io/en/latest/faq.html#what-parameters-should-i-use-for-my-reads

https://github.com/marbl/canu/issues/939

<hr />