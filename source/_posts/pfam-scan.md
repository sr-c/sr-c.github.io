---
title: 使用pfam-scan进行Pfam注释
tags: [Pfam,HMMER,pfam-scan,annotation]
date: 2018-10-14 17:05:09
permalink:
categories:
description:
---
<p class="description">使用pfam-scan进行Pfam注释</p>

<!-- more -->

## 安装

使用conda安装

```bash
conda create -n pfam_scan ##可新建一个环境，用于安装pfam-scan
source activate pfam_scan
conda install pfam_scan
```

`pfam_scan`依赖bioperl，因此，通过conda安装简单快捷

## 使用

首先，使用hmmer给数据库建立索引

```bash
hmmpress Pfam-A.hmm
```

执行程序

```bash
pfam_scan.pl -fasta <fasta_file> -dir <directory location of Pfam files> -outfile <file> -cpu <n> -as
```

## 为什么不直接使用hmmscan呢？

使用pfam-scan和直接使用hmmscan进行比对得到的输出有一定的不同。直接使用hmmscan比对得到的结果会允许不同上级分类的序列重叠，而pfam_scan.pl的输出指挥输出同一级分类下最显著（E-value最小）的结果。

>Q:  Why are the results from running hmmscan different from those I get when I run pfam_scan.pl?
>A:  We group together families which we believe to have a common evolutionary ancestor in clans.  Where there are overlapping matches within a clan, pfam_scan.pl will only show the most significant (the lowest E-value) match within the clan.  We perform the same clan filtering step on the Pfam website.  If you do want the script to report all the overlapping clan matches, you can use the -clan_overlap option.

## 参考来源

https://www.jianshu.com/p/396eaf5c1a83

http://www.oebiotech.com/Article/dbbmnlycrj.html

<hr />