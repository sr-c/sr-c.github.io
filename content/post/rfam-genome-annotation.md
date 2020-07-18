---
title: 使用Rfam进行ncRNA注释
tags: [Rfam,annotation,ncRNA,non-coding RNA]
date: 2018-10-19 16:02:44
permalink:
categories:
description:
---
<p class="description">使用Rfam进行non-coding RNAs的鉴定</p>

<!-- more -->

## Infernal

直接下载安装Infernal1.1.2的已编译版本

```bash
wget http://eddylab.org/infernal/infernal-1.1.2-linux-intel-gcc.tar.gz
tar xf infernal-1.1.2-linux-intel-gcc.tar.gz
echo 'PATH=$PATH:/your/path/to/infernal-1.1.2-linux-intel-gcc/binaries' >> ~/.bashrc
```

Rfam12.0是第一个基于Infernal1.1构建的版本，更新了注释流程。因此，更早版本的Rfam数据库只能通过Infernal1.0.2版本进行注释。

## 下载安装Rfam数据库

```bash
wget ftp://ftp.ebi.ac.uk/pub/databases/Rfam/CURRENT/Rfam.cm.gz
gunzip Rfam.cm.gz
wget ftp://ftp.ebi.ac.uk/pub/databases/Rfam/CURRENT/Rfam.clanin
# index the Rfam.cm file
cmpress Rfam.cm
```

## 计算目标基因组大小

```bash
esl-seqstat tutorial/mrum-genome.fa
$ Total # of residues: 2937203
```

Inferal需要根据输入数据的总碱基数设定E-value的阈值。因此，在执行`cmscan`之前需要先行计算总碱基数，乘以2（因为序列的两条链都会被搜索），再除以1,000,000得到以兆为单位的碱基数，作为`cmscan`的参数输入。

## 运行程序

```bash
cmscan -Z 5.874406 --cut_ga --rfam --nohmmonly --tblout mrum-genome.tblout --fmt 2 --cpu 8 --clanin Rfam.clanin Rfam.cm tutorial/mrum-genome.fa > mrum-genome.cmscan
```

## 参考来源

https://rfam.readthedocs.io/en/latest/genome-annotation.html

http://eddylab.org/infernal/Userguide.pdf

<hr />