---
title: xargs-tips
tags: [xargs,awk]
date: 2018-08-31 22:19:13
permalink:
categories:
description:
---
<p class="description">批量化生成脚本</p>

<!-- more -->

## 批量化生成trimmomatic运行脚本

对于paired-end数据，trimmomatic官方推荐的[运行方式](http://www.usadellab.org/cms/?page=trimmomatic)如下

> java -jar trimmomatic-0.35.jar PE -phred33 input_forward.fq.gz  input_reverse.fq.gz output_forward_paired.fq.gz  output_forward_unpaired.fq.gz output_reverse_paired.fq.gz  output_reverse_unpaired.fq.gz ILLUMINACLIP:TruSeq3-PE.fa:2:30:10  LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

其中输入输出的六个文件依次在命令行中排列，大量数据处理时，手动编辑的工作量巨大。

## 输出运行脚本

```bash
#ls *.fq.gz | xargs -i echo java -jar trimmomatic-0.35.jar PE {}_forward.fq.gz  {}_reverse.fq.gz {}_forward_paired.fq.gz {}_forward_unpaired.fq.gz {}_reverse_paired.fq.gz {}_reverse_unpaired.fq.gz ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:5 TRAILING:5 SLIDINGWINDOW:4:5 MINLEN:25 > trimmomatic.sh
awk '{print $2}' ../sample.txt | xargs -i echo java -jar trimmomatic-0.35.jar PE {}_forward.fq.gz  {}_reverse.fq.gz {}_forward_paired.fq.gz {}_forward_unpaired.fq.gz {}_reverse_paired.fq.gz {}_reverse_unpaired.fq.gz ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:5 TRAILING:10 SLIDINGWINDOW:4:5 MINLEN:36 > trimmomatic.sh
```

## 参考来源

http://www.genek.tv/article/25

<hr />