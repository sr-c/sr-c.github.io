---
title: HiC-Pro使用手册
tags: [Hi-C]
date: 2019-02-20 19:49:38
permalink:
categories:
description:
---
<p class="description"></p>

<!-- more -->

## 软件安装

软件默认提供了[Singularity](http://singularity.lbl.gov/install-linux)的镜像，可以直接使用

### 环境依赖

- The [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) mapper
- Python (>2.7) with *pysam (>=0.8.3)*, *bx-python(>=0.5.0)*, *numpy(>=1.8.2)*, and *scipy(>=0.15.1)* libraries.
   **Note that the current version does not support python 3**
- R with the *RColorBrewer* and *ggplot2 (>2.2.1)* packages
- g++ compiler
- samtools (>1.1)
- Unix sort (**which support -V option**) is required ! For Mac OS user, please install the GNU core utilities !

其中，python模块`pysam`与`bx-python`不太好安装，因此，还是使用conda来配置安装环境比较方便。

```bash
conda create -n hicpro python=2.7
source activate hicpro
conda install -y samtools bowtie2 R
conda install -y pysam bx-python numpy scipy

R
>install.packages(c('ggplot2','RColorBrewer'))
```

但是我们得到一条报错

```R
Warning messages:
1: In download.file(url, destfile = f, quiet = TRUE) :
  unable to load shared object '/Storage/data002/shurh/miniconda3/envs/hicpro/lib/R/modules//internet.so':
  libssl.so.1.0.0: cannot open shared object file: No such file or directory
2: packages ‘ggplot2’, ‘RColorBrewer’ are not available (for R version 3.5.1)
```

经过[错误排查](https://github.com/ContinuumIO/anaconda-issues/issues/8971)，我们需要升级`curl`

```bash
conda update curl
```

然后，我们就可以重新在R中安装`ggplot2`与`RColorBrewer`了

### 软件编译

解决了环境依赖之后，编译安装就很容易了

```bash
git clone https://github.com/nservant/HiC-Pro.git
cd HiC-Pro
vi config-install.txt
make configure
make install
```



## 测试使用

### 测试数据

```bash
wget https://zerkalo.curie.fr/partage/HiC-Pro/HiCPro_testdata.tar.gz && tar -zxvf HiCPro_testdata.tar.gz
wget ftp://ftp.ncbi.nlm.nih.gov/refseq/H_sapiens/annotation/GRCh38_latest/refseq_identifiers/GRCh38_latest_genomic.fna.gz
```

还可以使用其他已发表的数据来使用

```bash
prefetch SRR824846
wget ftp://ftp.ensemblgenomes.org/pub/bacteria/release-40/fasta/bacteria_20_collection/caulobacter_crescentus_na1000/dna/Caulobacter_crescentus_na1000.ASM2200v1.dna.toplevel.fa.gz
gunzip Caulobacter_crescentus_na1000.ASM2200v1.dna.toplevel.fa.gz
bowtie2-build Caulobacter_crescentus_na1000.ASM2200v1.dna.toplevel.fa bacteria
cat >genome.size
Chromosome 4016942
```

### 得到注释文件

`HiC-Pro`需要两个注释文件，分别包含实验对基因组的酶切位点，以及基因组的染色体长度。

#### restricttion fragments file

该文件可以由HiC-Pro提供的一支程序得到

#### 染色体长度

```R
###
## How to generate chromosome size files ?
###
require(Biostrings)

TargetGenome <- readDNAStringSet("genome.fasta", format = "fasta")
chrom.size <- seqlengths(TargetGenome)
write.table(chrom.size, file="genome.size", quote=FALSE, col.names=TRUE, sep="\t")
```

## 参考来源

http://nservant.github.io/HiC-Pro/

https://cloud.tencent.com/developer/article/1178446

https://github.com/JiFansen/Blog/issues/5

https://zhuanlan.zhihu.com/p/48455671

https://cloud.tencent.com/developer/article/1178442

<hr />