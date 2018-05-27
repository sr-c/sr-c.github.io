---
title: RepeatModeler与RepeatMasker 重复序列分析
tags: software
date: 2018-05-27 13:23:17
categories:
description: 
---
<p class="description">RepeatMasker/RepeatModeler 是用于重复序列分析的常用软件。其运行需要调用trf, hmmer,  RECON, RepeatScout等其他软件，还需要调用ABBlast/rmblastn/WUBlast作为序列比对的引擎。</p>
<!-- more -->
相对于rmblastn，选用ABBlast作为序列搜索引擎时，有一点需要注意。

## 建立数据库
```shell
${RepeatModelerPath}/BuildDatabase -name ${database_name}  ${fasta}
```
但是对于ABBlast来说，`BuildDatabase`建立的索引还缺少nt_db.xni文件，出现提示
```shell
FATAL:  xdf_db_fopen failed code 22 (identifier index does not exist) on
        database "nt_db":  the file "nt_db.xni" was not found.  To create a
        sequence identifier index, execute xdformat with the -I option or
        re-index the existing database (faster) using the -X option.
```
需要手动调用ABBlast中的`xdformat`来建立xni索引

```shell
xdformat -n -I nt_db
```
## 构建LIBRARY 

运行`RepeatModeler`主程序

```shell
${RepeatModelerPath}/RepeatModeler -pa 30 -database ${database_name}>& run.out &
```
结果文件夹种的`consensi.fa.classified`可以作为library用于RepeatMasker进行重复序列的屏蔽。
## 重复序列的计算与屏蔽
```shell
$RepeatMasker -pa 16 \
  -lib consensi.fa.classified \
  -dir Repeat_result -html -gff species.genome.fasta
```

注意，RepeatMasker的结果文件夹`Repeat_result`需要提前手动建立，否则程序运行完成后结果文件会丢失。

无library直接使用RepeatMasker中的RepBase数据库来计算重复序列，若RepBase数据库对目标物种的覆盖不好，则很可能只找到较少的重复序列。此时，使用RepeatModeler构建library就很有必要。

## 参考来源

> https://logozy.me/an-zhuang-he-shi-yong-repeatmodler/
> http://etutorials.org/Misc/blast/Part+V+BLAST+Reference/Chapter+14.+WU-BLAST+Reference/14.4+xdformat+Parameters/

<hr />