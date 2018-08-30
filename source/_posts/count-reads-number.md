---
title: 快速了解fastq.gz文件中的reads数目
tags: [fastq,qc]
date: 2018-08-30 16:17:04
permalink:
categories:
description:
---
<p class="description">对测序的原始数据进行质控，需要快速查看测序的数据量是否达到预期的标准</p>

<!-- more -->

## 查看单个fq.gz文件中的reads数目

使用`zcat`命令即可直接查看fq.gz文件的内容。而fastq文件中，每一条read记录占用4行。因此，查看单个文件的reads数目可如下实现

```bash
zcat your_raw_data.fastq.gz | grep -c '+'
```

或者统计文件内容的行数，除以4即为reads数目。

```bash
zcat your_raw_data.fastq.gz | wc -l
```

### 直接通过fq.gz文件大小进行判断

参考[Lablueee](http://www.zxzyl.com/archives/1011)同志的研究，`gzip`的压缩效率对于同一测序平台的数据文件效率相近，因此可通过数据文件的大小与数据量的线性关系推断测序数据量。

>因为R1和R2数据量相同的原因，我只看R1的真实文件和gz文件大小与数据量之间的关系。
>
>数据量=FASTQ文件行数/4*151/1000/1000  单位为M 
>
>真实文件大小估计=FASTQ文件行数/4*357/1024/1024 单位为M，预测值，差别不大，因为FASTQ文件中每四行357个字符（和平台和设置有关系），每个字符1byte。 
>
>GZ文件大小通过ll -h查看  
>
>因为FASTQ文件是规范的，每四行字符基本一致，所以FASTQ真实文件大小和数据量成正比。比如我前面提到的每四行有357个字符，其中序列只占151个字符，也就是说FASTQ文件大小大概是测序量的357/151≈2.3倍多。但因为FASTQ文件为文本文件，占用空间较大，所以一般将FASTQ文件压缩成gzip格式文件。 

根据其线性拟合结果，（对于PE150数据）Miseq R1文件的数据量大概是gzip文件的1.242倍（1/0.35/2.3），Hiseq约在1.89(1/0.23/2.3)倍。 

## 查看多个fq.gz文件中的reads数目

```bash
zcat ???.cb_R1.fastq.gz | grep -c '^+'   > AAA.txt
```

或者直接写一个循环：

```bash
ls *fastq.gz|while read i;do echo "$i";zcat $i |wc -l ;done
```

## 参考来源

http://www.omicsclass.com/question/145

http://www.zxzyl.com/archives/1011

<hr />