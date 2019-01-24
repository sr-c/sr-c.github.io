---
title: 使用salmon计算RNA-seq表达量矩阵
tags: [salmon,RNA-seq]
date: 2019-01-24 18:56:56
permalink:
categories:
description:
---
<p class="description">使用salmon快速计算RNA-seq数据的表达量矩阵</p>

<!-- more -->

传统的RNA-seq分析方法需要将reads比对到基因组上，然后再统计每个基因的counts，进而计算TPM/FPKM。现在，发展出了许多alignment-free的方法，不需要进行比对，直接统计出count矩阵。

## 安装

salmon直接提供了linux的预编译版本，解压后可直接使用。

```bash
wget https://github.com/COMBINE-lab/salmon/releases/download/v0.12.0/salmon-0.12.0_linux_x86_64.tar.gz
```

## 准备输入数据

1. 参考转录组，即目标物种的cdna序列

2. 比对文件(**alignment**-based mode)或测序文件(**quasi-mapping**-based mode)

   是的，salmon可以不经过比对，直接以测序数据作为输入

## 建立索引

```bash
salmon index -t transcripts.fasta -i transcripts_index --type quasi -k 31

# -t： 参考转录组路径，支持压缩文件
# -i： 设定index名称
# -type： 索引类型，分为fmd, quasi, 建议quasi
# -k: k-mers的长度。说明文档中，若reads长度大于75 bp，取值31可获得较好的效果

salmon index -t transcripts.fasta -i transcripts_index
# 全部使用默认参数也可创建索引
```

## 定量计算

```bash
salmon quant -i transcripts_index  -l A -1 reads_1.fastq -2 reads_2.fastq -o transcripts_quant

# -i： 上一步建立好的index路径
# -l/--libType: 文库类型，详细参见文档，可设置为A，使用软件自动适配
# -1： read1，支持压缩文件
# -2： read2，支持压缩文件
# -o： 输出目录
```

## 参考来源

https://salmon.readthedocs.io/en/latest/salmon.html

https://www.jianshu.com/p/7564eca62354

http://www.bioinfo-scrounger.com/archives/411

http://blog.sciencenet.cn/blog-3334560-1084369.html

<hr />