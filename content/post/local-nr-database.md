---
title: 本地化nr数据库
tags: [nr,ncbi,blast,gi,taxid]
date: 2018-10-02 12:19:26
permalink:
categories:
description:
---
<p class="description">本地化nr数据库</p>

<!-- more -->

## 数据库下载

nr数据库下载前应确认其序列ID中包含有`gi`信息。推荐通过NCBI提供的[脚本](https://www.ncbi.nlm.nih.gov/viewvc/v1/trunk/c++/src/app/blast/update_blastdb.pl)进行下载。

```bash
perl update_blastdb.pl nr
```

下载完成后解压

```bash
tar -zxvf *.tar.gz 
```

## 建库

由于下载的是nr数据库建库后的文件，因此不需要再通过makeblastdb进行建库。

此外，如果想要在blast结果中添加物种名称，则需要手动下载nr数据(FASTA格式)以及gi与taxid的[映射文件](ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/gi_taxid_nucl.dmp.gz)，在makeblastdb时通过`-taxid_map gi_taxid_nucl.dmp`参数将映射信息包含在库中。

## 其他

同时，使用`blastdbcmd`可以从库文件中提取序列

```bash
blastdbcmd -db nr -dbtype prot -entry all -outfmt "%f" -out nr.fa 
```

使用`blast_formatter`可以将asn格式(outfmt 11)转换为其他格式

```ba&#39;s
blast_formatter -archive "test.blastn@nr.asn" -outfmt "7 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore staxids salltitles" > "test.blastn@nr.tab"
```

## 参考来源

http://blog.shenwei.me/local-blast-installation/

https://segmentfault.com/a/1190000012055972

<hr />