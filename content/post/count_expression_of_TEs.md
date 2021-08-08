---
title: "计算转录组数据中转座子的表达量"
date: 2021-03-10T16:57:38+08:00
tags: [RNA-seq, STAR, RepeatMasker]
categories: []
draft: true
---

统计样品中转座子的转录情况

<!-- more -->

参照某些情况下会要求统计转座子的转录情况。可以使用`STAR`比对的`--quantMode GeneCounts`便捷地统计比对到转座子的reads数目。

## 构建STAR索引

基于RepeatMasker的输出，构建一个TE的gtf文件适应STAR 的要求。

STAR 构建索引时可以不输入注释文件，但之后需要在比对时再指定注释文件。

默认情况下，`--runMode genomeGenerate` 通过`--genomeFastaFiles` 输入基因组文件，通过`--sjdbGTFfile` 输入gtf注释文件。STAR 只会读入`exon`项，因此其他项可忽略。

> gff3文件也可以通过`--sjdbGTFfile` 参数输入，但还需要通过`--sjdbGTFtagExonParentTranscript`输入第9列中exon对应的Parent字段，默认就是`parent`。

因此，可通过RepeatMasker输出的gff文件，稍作修改，成为需要的gtf文件。注意，gtf文件的第9列至少需要存在`gene_id`与`transcript_id`字段。

## 比对并计数

通过`--quantMode GeneCounts` 参数，即可在比对结果的同时输出对应的` ReadsPerGene.out.tab`文件。与注释区域存在唯一匹配，且至少1 nt 重叠的read即会被计数。

```bash
STAR \
--runThreadN 16 \
--runMode alignReads \
--readFilesCommand zcat \
--outSAMtype BAM Unsorted SortedByCoordinate \
--quantMode GeneCounts \
--outFileNamePrefix $outputPrefix \
--genomeDir $indexedreference \
--readFilesIn $inputfile1 $inputfile2
```

其中统计了比对至转录本的项目文件，内容分为4列，如下

>column 1: gene ID
>column 2: counts for unstranded RNA-seq
>column 3: counts for the 1st read strand aligned with RNA (htseq-count option -s yes)
>column 4: counts for the 2nd read strand aligned with RNA (htseq-count option -s reverse)



## 第三方程序

使用`--quantMode TranscriptomeSAM GeneCounts`模式，在输出` ReadsPerGene.out.tab`的同时，还会输出比对至转录本的bam文件`Aligned.toTranscriptome.out.bam`. 这个文件可以直接用于下游的转录本计数软件，如RSEM 或eXpress.

```bash
rsem-calculate-expression ... --bam Aligned.toTranscriptome.out.bam /path/to/RSEM/reference RSEM
```

## 参考来源

https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf

https://github.com/alexdobin/STAR/issues/84