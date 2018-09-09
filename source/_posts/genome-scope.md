---
title: 使用GenomeScope进行基因组评估
tags: [GenomeScope,Jellyfish]
date: 2018-08-30 20:32:53
permalink:
categories:
description:
---
<p class="description">使用GenomeScope预估基因组的参数，主要包括基因组大小，杂合率等信息。</p>

<!-- more -->

## 使用Jellyfish 统计k-mer

1. 下载并安装: <http://www.genome.umd.edu/jellyfish.html#Release> 

2. 准备数据

   参照陈连福的[方法](http://www.chenlianfu.com/?p=806)，提前将paired-end数据整合。将read2数据的序列反向重复后与read1文件合并。这一步使用Trinity附带的fastool程序完成转换。

```bash
$ zcat read_1.clean.fq.gz | $Trinity_Home/trinity-plugins/fastool/fastool --illumina-trinity --to-fasta > reads_1.fasta
$ zcat read_2.clean.fq.gz | $Trinity_Home/trinity-plugins/fastool/fastool --rev --illumina-trinity --to-fasta > reads_2.fasta
$ cat reads_1.fasta reads_2.fasta > both.fasta
```

3. 统计kmers 

```bash
$ jellyfish count -C -m 21 -s 1000000000 -t 10 both.fasta -o reads.jf
```

4. 绘制直方图 

```bash
$ jellyfish histo -t 10 reads.jf > reads.histo
```

>-m是kmer长度；
>
>-s是预估哈希表的大小，即G+G*c*e*k。G是Genome Size；c是coverage（genome survey测序通常低于100x）；e是测序错误率（illumina为1%）；k是kmer大小。
>
>-C表示考虑DNA正义与反义链，遇到反义kmer时，计入正义kmer频数中。

## Running GenomeScope Online

在线作图http://genomescope.org/

## Running GenomeScope on the Command Line

```bash
$ Rscript genomescope.R histogram_file k-mer_length read_length output_dir [kmer_max] [verbose]
```

## 参考来源

https://github.com/schatzlab/genomescope

https://gif.biotech.iastate.edu/genomescope

http://www.chenlianfu.com/?p=806

https://fengleiblog.wordpress.com/2014/12/17/%E5%9F%BA%E4%BA%8Eillumina%E6%B5%8B%E5%BA%8F%E6%95%B0%E6%8D%AE%EF%BC%8C%E4%BD%BF%E7%94%A8jellyfish%E4%BC%B0%E8%AE%A1%E5%9F%BA%E5%9B%A0%E7%BB%84%E5%A4%A7%E5%B0%8F/

<hr />