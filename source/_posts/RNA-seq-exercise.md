---
title: RNA-seq小考核
tags: [RNA-seq,生信技能树,biotrainee]
date: 2019-03-11 20:54:10
permalink:
categories:
description:
---
<p class="description">生信技能树的single cell RNA-seq小考核</p>

<!-- more -->

### Q1: 参考基因组及注释文件下载地址
列出人，小鼠，拟南芥的基因组序列，转录组cDNA序列，基因组注释gtf文件下载地址

> **[Human (GRCh38.p12)](http://asia.ensembl.org/Homo_sapiens/Info/Index?db=core)**
>
> ftp://ftp.ensembl.org/pub/release-95/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.toplevel.fa.gz
>
> ftp://ftp.ensembl.org/pub/release-95/fasta/homo_sapiens/cdna/Homo_sapiens.GRCh38.cdna.all.fa.gz
>
> ftp://ftp.ensembl.org/pub/release-95/gtf/homo_sapiens/Homo_sapiens.GRCh38.95.gtf.gz
>
> **[Mouse (GRCm38.p6)](http://asia.ensembl.org/Mus_musculus/Info/Index?db=core)**
>
> ftp://ftp.ensembl.org/pub/release-95/fasta/mus_musculus/dna/Mus_musculus.GRCm38.dna.toplevel.fa.gz
>
> ftp://ftp.ensembl.org/pub/release-95/fasta/mus_musculus/cdna/Mus_musculus.GRCm38.cdna.all.fa.gz
>
> ftp://ftp.ensembl.org/pub/release-95/gtf/mus_musculus/Mus_musculus.GRCm38.95.gtf.gz
>
> [**Arabidopsis thaliana (TAIR10)**](https://plants.ensembl.org/Arabidopsis_thaliana/Info/Index?db=core)
>
> ftp://ftp.ensemblgenomes.org/pub/plants/release-42/fasta/arabidopsis_thaliana/dna/Arabidopsis_thaliana.TAIR10.dna.toplevel.fa.gz
>
> ftp://ftp.ensemblgenomes.org/pub/plants/release-42/fasta/arabidopsis_thaliana/cdna/Arabidopsis_thaliana.TAIR10.cdna.all.fa.gz
>
> ftp://ftp.ensemblgenomes.org/pub/plants/release-42/gtf/arabidopsis_thaliana/Arabidopsis_thaliana.TAIR10.42.gtf.gz

### Q2: 找到文章的测序数据
2018年12月的NC文章：Spatially and functionally distinct subclasses of breast cancer-associated fibroblasts revealed by single cell RNA sequencing 使用成熟的单细胞转录组( Smart-seq2 )手段探索了癌相关的成纤维细胞 CAFs的功能和空间异质性。

```R
# 根据Data availability，数据位于GEO, GSE111229
```
### Q3：下载测序数据
主要是理解GEO链接： GSE111229 和原始测序数据：SRP133642 两个链接
```R
library(GEOquery)
gset = getGEO('GSE111229')
exprSet = exprs(gset[[1]])
```

```bash
# 直接下载
wget -c ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE111nnn/GSE111229/matrix/GSE111229_series_matrix.txt.gz
for i in `cat SraAccList.txt`; do prefetch $i; done
```

## 参考来源

<hr />