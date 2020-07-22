---
title: "搜索简单基因组中的重复序列"
date: 2020-07-22T11:46:46+08:00
tags: [python]
categories: [Genomics] 
---

分析原核/病毒基因组中的重复序列

<!-- more -->

# 寻找基因间区

首先，[计算](https://sr-c.github.io/2020/06/07/cds-coverage/)基因组中的基因编码区。然后，通过interval取反得到基因间区。若只为得到基因间区，重叠的CDS区域不用合并，并不影响与基因组全长取差。

```python
from interval import Interval, IntervalSet 
f = open("XGV_0.7.2.cds.intervals", "r")
cds = f.readlines()
f.close
cdIn = []
for i in cds:
   [s, e] = i.strip().split("\t")
   cdIn.append(Interval(int(s), int(e)))

## Get intervals for all CDS    
allcds = IntervalSet(cdIn)

## Define IntervalSet for the genome.
full_len = IntervalSet([Interval(1, 116875)])

## ^ get the symmetric difference
outCDS = full_len ^ allcds
print(outCDS)
```

# 检测简单重复序列

TRF, REPuter是常用的检测简单重复序列的工具，可在全基因组范围内寻找，也可针对特定基因间区进行搜索。

TRF一般用于寻找串联重复序列，可使用在线服务。

REPuter不仅可寻找串联重复，对于4种类型的重复序列（如下）均可检索。使用比勒费尔德大学生信中心提供的[在线服务](https://bibiserv.cebitec.uni-bielefeld.de/reputer?id=reputer_manual_manual)，结果可通过网页可视化。

1. forward(direct) match                       
![forward match](https://bibiserv.cebitec.uni-bielefeld.de/applications/reputer/resources/images/forward.png) 
2. reverse match
![reverse match](https://bibiserv.cebitec.uni-bielefeld.de/applications/reputer/resources/images/reverse.png)
3. complement match 
![complement match](https://bibiserv.cebitec.uni-bielefeld.de/applications/reputer/resources/images/complement.png)
4. palindromic match
![palindromic match](https://bibiserv.cebitec.uni-bielefeld.de/applications/reputer/resources/images/palindromic.png)

​            

blast2seq 是NCBI较为古老的BLAST工具中的一员。可直接对两段序列进行比较，不需要`make database`. 对同一段序列进行自比较，并过滤去同位置完全匹配的结果，即可用于发现其中存在的重复序列。

# 检测回文序列

EMBOSS palindrome可检测序列内部的回文序列。通过参数可设定回文序列的长度范围，错配的碱基数。

# 参考来源

https://pythonhosted.org/intervalset/