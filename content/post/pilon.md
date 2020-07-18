---
title: Pilon的使用
tags: [Pilon]
date: 2018-07-16 20:44:35
permalink:
categories:
description:
---
<p class="description">`Pilon`是用来提升基因组组装质量，寻找品种间变异的工具。</p>

<!-- more -->

`Pilon`使用read比对分析识别输入的基因组序列与reads证据之间的不一致，尝试修正基因组序列中的差错，包括：

>- 单碱基变异
>- 小的插入缺失
>- 较大的插入缺失或片段替换事件（block substitution events ）
>- 补洞
>- 识别错误的本地组装，包括打开新的gaps

## 输入数据

`Pilon`的输入数据有两个，`FASTA`格式的基因组文件以及将测序数据比对到基因组上的`BAM`文件。其中，`BAM`文件需要提前sort并建立索引（be sorted in coordinate order and indexed）。对于Illumina数据，一般使用`BWA`或`Bowtie 2`进行序列比对。

> To use Pilon with default arguments, read length is recommended to be 75 bases or longer and total sequence coverage should be 50x or greater,  though deeper total coverage of >100x is beneficial. 

要使用默认参数运行`Pilon`，推荐输入数据的reads读长大于75 bp，全序列的覆盖度应大于50x，当然覆盖度更高（大于100x）就更好了。Pilon能够使用小片段，大片段或单端测序数据作为输入，并且可以同时输入多个文库的数据。Pilon能够有效地利用reads之间的配对信息，因此强烈建议以双端测序文库数据作为输入。

## 输入文件

Pilon产生一个修正后的基因组序列，包括了所有的单碱基变异，Indel等修正。

Pilon能产生一个[VCF格式](http://samtools.github.io/hts-specs/VCFv4.1.pdf )的文件，记录了基因组中每个位点的SNP或InDel信息。经本地重新组装得到的变化记录在相应的标记(SVTYPE=INS and SVTYPE=DEL) 中。Pilon还能够产生一个“changes”文件，以表格方式记录了从输入基因组到输出基因组之间的编辑操作，两者之间的一致性序列，以及两个各自的序列。最后，Pilon还能够产生一系列可视化的记录文件 (“bed” and “wig” files)  ，用于诸如IGV(17)和GenomeView(18)基因组浏览器的可视化。记录文件包括了序列的覆盖度（sequence coverage and physical coverage），每个位点匹配到的有效reads的百分比。

Pilon的标准输出也含有许多有用的信息，包括覆盖度，输入基因组的确认度，修正信息的汇总，以及一些特殊标记，却未得到修正的问题。

## 资源消耗

> Larger genomes will require more memory to process; exactly how much is  very data-dependent, but as a rule of thumb, try to allocate 1GB per  megabase of input genome to be processed. 

因此，100M的基因组就约需要100G的内存消耗。105M基因组，输入`clean data`5G数据量实测内存占用峰值达到82.5G，对于大型基因组来说，消耗惊人。

## 参考来源

https://github.com/broadinstitute/pilon/wiki/Requirements-&-Usage

https://github.com/broadinstitute/pilon/wiki/Methods-of-Operation

<hr />