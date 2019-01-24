---
title: 使用STAR判断测序文库的链特异性
tags: [STAR, transcript, dUTP, IGV,]
date: 2018-11-06 10:39:42
permalink:
categories:
description:
---
<p class="description">判断测序文库的链特异性</p>

<!-- more -->

## 安装

```bash
# Get latest STAR source from releases
wget https://github.com/alexdobin/STAR/archive/2.6.1c.tar.gz
tar -xzf 2.6.1c.tar.gz
cd STAR-2.6.1c

# Compile
cd STAR/source
make STAR

#在bin/目录下就已存在对应平台的已编译版本，可直接使用
```

## 使用

### 构建索引

```bash
STAR --runMode genomeGenerate \
--genomeDir ~/reference/STAR_index/ \
--genomeFastaFiles ~/reference/genome/hg38/Homo_sapiens.GRCh38.dna.toplevel.fa \
--sjdbGTFfile ~/reference/genome/hg38/Homo_sapiens.GRCh38.90.chr_patch_hapl_scaff.gtf \
--sjdbOverhang 149
```

### 进行比对

```bash
STAR --runThreadN 20 --genomeDir ~/reference/index/STAR/mm10/ \
--readFilesIn SRR3589959_1.fastq SRR3589959_2.fastq \
--outSAMtype BAM SortedByCoordinate \
--outFileNamePrefix ./SRR3589959
```

## 注意事项

比对时候需要注意内存限制，否则可能得到报错

```bash
Nov 07 16:46:03 ..... started sorting BAM
Max memory needed for sorting = 2566682989

EXITING because of FATAL ERROR: number of bytes expected from the BAM bin does not agree with the actual size on disk: 1613446932   0   45

Nov 07 16:46:03 ...... FATAL ERROR, exiting
```



## 查看比对结果

使用IGV查看结果

### 向IGV中导入参考基因组

> Genomes -> Creat a .genome file...

在弹出窗口中加入基因组序列fasta文件与基因组结构注释gff/gtf文件

### 导入比对文件

> File -> Load from File...

比对的bam/sam文件需要具有索引

### 检验转录组文库是否具有链特异性

首先，需要辨明正反链，正义/反义链，编码链，模板链的概念。

DNA 的正链和负链，就是那两条反向互补的链。参考基因组给出的那个链就是所谓的正链（forword），另一条链是反链（reverse）。但是这正反一定**不能和正义链（sense strand）反义链（antisense strand）混淆**，两条互补的DNA链其中一条携带编码蛋白质信息的链称为正义链，另一条与之互补的称为反义链。但是携带编码信息的正义链不是模板，只是因为它的序列和RNA相同，正义链也是编码链。而反义链虽然和RNA反向互补，但它可是真正给RNA当模板的链，因此反义链也是模板链。

**总结两点**

1. **正义链（sense strand）= 编码链（coding strand）= 非模板链**
2. **forword strand 上可以同时有sense strand 和 antisense strand。因为这完全是两个不同的概念。**

在IGV的**Read strand**模式中，显示的reads分为红蓝两色，其中红色代表read方向与DNA正链方向相同(5' -> 3')，蓝色代表read方向与DNA正链方向相反

在**First-of-pair strand**模式中，红色代表成对的reads中，第一链的方向与正链相同(5' -> 3')，蓝色代表成对的reads中，第一链的方向与正链相反(5' -> 3')。这对于展示链特异性的文库特别有帮助。

>For a given transcript, non-directional libraries will show a mix of red and blue reads aligning to the locus.
>
>Directional libraries will show reads of one color in the direction matching the transcript orientation.

对于非链特异性的文库，匹配到同一个基因的reads会表现出红蓝混合的情况；对于链特异性的文库，匹配到同一个基因的reads则会表现出与转录本方向相匹配的颜色。

[判断测序文库是否链特异性的其他方法](http://www.omicsclass.com/article/300)

## 参考来源

https://github.com/alexdobin/STAR

https://www.jianshu.com/p/eca16bf2824e

https://www.jianshu.com/p/a63595a41bed

http://kaopubear.top/2017-10-23-ssrnaseqbasic.html

<hr />