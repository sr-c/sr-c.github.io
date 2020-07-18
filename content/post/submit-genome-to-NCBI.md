---
title: 向GenBank提交基因组数据
tags: [NCBI]
date: 2020-02-25 23:31:22
permalink:
categories: [Genomics]
description:
---
<p class="description"></p>
<!-- more -->

向NCBI提交基因组数据的关键在于提供符合NCBI规范的注释信息。NCBI要求其专门的5列的注释形式，以`.tbl`文件提供。当时该文件的生成比较

# 获取locus_tag

`locus_tag`其实是一个定位的标签，指向所提交基因组信息的`BioProject`与`BioSample`组合。在申请完成BioProject与BioSample后，会在NCBI返回的邮件中告知。若没有收到，也可以邮件向NCBI询问。

# 准备序列文件

## Fasta文件

如果没有基因组的注释信息，则可以直接提交fasta文件。

## .sqn文件

具有注释信息的基因组序列，应当以.sqn文件提交。

准备.sqn文件需要的

- 序列fasta文件
- 数据提交方和出版方信息的[模板文件](https://submit.ncbi.nlm.nih.gov/genbank/template/submission/)
- 注释文件
- 序列质量文件（可选）

其中，最关键的注释文件，可以以NCBI要求的5列的列表文件给出。现在，可以通过比较常见的`gtf`或`gff`文件形式给出，使用`table2asn_GFF`工具整合注释信息。

### 校验注释文件完整规范

通过`genometools`提供的`gff`工具可检验gff文件是否符合规范。

### GenBank对注释信息的要求

1. GFF3/GTF文件中第一列的序列名称必须与fasta文件中的序列名称一致
2. contig, supercontig, chromosome 等信息并不必要，而且在整合过程中被忽略。
3. gene和mRNA注释可以提供，但不是必须。只有CDS信息是必须的，而其他信息会根据CDS信息自动生成。

# 准备注释信息

# 检验报错信息

## 报错处理

发现的错误往往包括[许多类型](https://www.ncbi.nlm.nih.gov/genbank/asndisc/#evaluating_the_output)。但应当保证其中没有ERROR

## 参考来源

https://www.ncbi.nlm.nih.gov/genbank/genomesubmit/

https://www.ncbi.nlm.nih.gov/genbank/genomes_gff/

<hr />