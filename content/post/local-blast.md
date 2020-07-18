---
title: 本地blast
tags: [blast]
date: 2018-06-23 14:35:06
permalink:
categories:
description:
---
<p class="description">blast的本地化运行</p>

<!-- more -->

## 安装

从ncbi[下载](ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/)已编译好的版本，解压缩后即可使用。

## 使用

### Building a BLAST database with local sequences

```bash
$ makeblastdb -in mydb.fsa -parse_seqids -dbtype nucl

Building a new DB, current time: 01/28/2011 13:39:37
New DB name:   mydb.fsa
New DB title:  mydb.fsa
Sequence type: Nucleotide
Keep Linkouts: T
Keep MBits: T
Maximum file size: 1073741824B
Adding sequences from FASTA; added 3 sequences in 0.00206995 seconds.
$ 
```

参数说明：

```bash
-in 需要建库的fasta序列
-dbtype 如果是蛋白库则用prot，核酸库用nucl
-out 所建数据库的名称
-parse_seqids => Parse Seq-ids in FASTA input         
```

`-parse_seqids` 和`-out uniprot` 一般都加上，使用`-parse_seqids`需求数据库中每条序列都有唯一的标识，使得blast能够根据序列标识来提取序列。

### Use Windowmasker to filter the query sequence(s) in a BLAST search

```
1. Run BLAST search using Windowmasker for sequence filtering based upon taxid 
(9606 is the taxid for human).
$ blastn -query input -db database -window_masker_taxid 9606 -out results.txt

2. Run BLAST search using Windowmasker for sequence filtering based upon the windowmasker file name.
$ blastn –query input –db database –window_masker_db 9606/wmasker.obinary
```

### Quick start

A BLAST search against a database requires at least a –query and –db option. The command:

```
blastn –db nt –query nt.fsa –out results.out  
```

参数说明

```
-query 输入文件名，也就是需要比对的序列文件
-db 格式化后的数据库名称
-evalue 设定输出结果中的e-value阈值
-out 输出文件名
-num_alignments 输出比对上的序列的最大值条目数
-num_threads 线程数
此外还有：
-num_descriptions 对比对上序列的描述信息，一般跟tabular格式连用
-outfmt      
 0 = pairwise,
 1 = query-anchored showing identities,
 2 = query-anchored no identities,
 3 = flat query-anchored, show identities,
 4 = flat query-anchored, no identities,
 5 = XML Blast output,
 6 = tabular,
 7 = tabular with comment lines,
 8 = Text ASN.1,
 9 = Binary ASN.1
 10 = Comma-separated values
格式5，输出的内容最丰富，以标记语言写入，方便根据需要提取信息；
格式6，以表格形式输出，易于阅读。
```

## 参考来源

https://www.ncbi.nlm.nih.gov/books/NBK279688/

http://www.bioinfo-scrounger.com/archives/77

<hr />