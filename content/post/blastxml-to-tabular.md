---
title: blast结果格式转换
tags: [blast,python]
date: 2018-09-27 21:49:48
permalink:
categories:
description:
---
<p class="description">将blast的xml结果文件转换为表格格式</p>

<!-- more -->

## 解决方案

目前找到最简便的[解决方案](https://github.com/peterjc/galaxy_blast/raw/master/tools/ncbi_blast_plus/blastxml_to_tabular.py)

其他方案都依赖使用Bioperl, BioPython, BioRuby等解决方案，不够灵活

[网页版](http://159.149.160.68/galaxy/root?tool_id=toolshed.g2.bx.psu.edu%2Frepos%2Fdevteam%2Fncbi_blast_plus%2Fblastxml_to_tabular%2F0.1.01)解决方案，其中还有许多丰富功能，有待发现

## 使用方法

```bash
python blastxml_to_tabular.py -o output.tabular -c std input.xml
```

其中`std`代表标准的12列格式，`ext`代表具有额外信息的25列格式

## 参考来源

http://seqanswers.com/forums/showthread.php?t=29799

https://www.biostars.org/p/7290/

<hr />