---
title: 使用SERF鉴定端粒序列
tags: [telomere,端粒]
date: 2019-03-08 20:13:12
permalink:
categories:
description:
---
<p class="description">使用SERF在线工具从头预测端粒序列</p>

<!-- more -->

## 端粒重复序列

[端粒](https://en.wikipedia.org/wiki/Telomere)是染色体末端的一端核酸序列，起着保护染色体末端免于降解，防止与相邻的染色体错误融合的作用。其名称来源于希腊语 *τέλος* (end)和*μέρος* (part) 。对于脊椎动物，端粒的序列是`AGGGTT`，其对应的互补序列就是`TCCCAA`，还有这一个单链的`TTAGGG`末尾。这段`TTAGGG`的序列在人类基因组中约重复2500次。人类的平均端粒长度由出生时的约11 kb，在老年时降至短于4 kb. 而男性端粒的平均缩短速度要快于女性。

在染色体复制时，DNA复制酶在染色体末端不能继续复制，因此在每次复制时，染色体的末端都会缩短。这是由于冈崎片段需要RNA引物连接到后随链的前端。在细胞分裂时，端粒是染色体末端的缓冲区；防止基因被截短。端粒本身被端粒保护蛋白复合体，还有端粒DNA编码的RNA覆盖。

不同物种的端粒序列不同，目前已知的端粒序列数据在[端粒数据库](http://telomerase.asu.edu/sequences_telomere.html)中可以访问，目前的版本在2018年8月更新。

## 预测物种的端粒序列

对于一个端粒序列仍然未知的物种，我们怎么发现其端粒的序列呢？

在最近发表的一篇[文章](https://www.future-science.com/doi/full/10.2144/btn-2018-0057#)指出，目前由bioserf.org提供的在线服务 sequence repeat finder (SERF) 可以进行。用户提供全基因组组装的结果（FASTA 格式）或二代测序的短读长数据（FASTQ 格式）即可。用户在注册后即可使用。

### 输入参数
||Telomere Identification Protocol|
| ------------- | ------------------------------------------------------------ |
| FASTQ         | Input Reads from NGS                                         |
| FASTA         | Input Assembled Contigs.                                     |
| Contiguousall | Analysis Type                                                |
| TG            | BaseType (must contain these base types in the identified repeats) |
| 2             | BaseTypeMincount (must have atleast 2 of either type in the identified repeat) |
| 3             | MinRepeats to treat as tandem                                |
| 5             | MinBPSize of repeat motif                                    |
| 15            | MaxBPSize of repeat motif                                    |
| 90%           | MatchQuality (Tandem repeats to cover atleast 90% of the entire read. **Iterate with lower figure until repeats are identified**.) |
| 2000          | Base count to split large assembled contigs in FASTA file for faster results. Defaults to 0 for FASTQ reads. |
| Yes           | Permutate                                                    |
| Yes           | Exclude Homopolymers                                         |
| Yes           | Exclude Supersets                                            |
| Yes           | List Cluster Totals                                          |
| Notes         | For large input  data (1GB+), analyze in smaller minbp-maxbp chunks 7-10, 11-20, 21-30  etc (instead of 7-60) to see incremental results |

  ### 输出结果

这是示例公共数据中洋葱的输出结果

```
Results file (last 20 lines):
# ANALYSIS BEGIN Fri Mar 8 01:54:00 2019
# Input = BSF1-AlliumCepa.fastq.gz
# Output = Results-32188243-3-5-15-Q90-TG-2-contiguousall.txt

# SEGMENT-WISE RESULTS
# Motif, Repeats, Position, Size, Bases, Quality, Segment#, SegmentSize
TGGGCTCGGTTA, 3, 1, 12, 36, 94.7, 304572, 38
TCGGTTATGGGC, 3, 1, 12, 36, 92.3, 317960, 39


# CLUSTER BY POSITION
# Motif, Repeats, Repeats%, Bases, Bases%, Genome%, Position, Size
TGGGCTCGGTTA, 3, 50.0, 36, 50.0, 0.0, 1, 12
TGGGCTCGGTTA, 3, 50.0, 36, 50.0, 0.0, 1, 12


# CLUSTER TOTALS
# Motif, Repeats, Repeats%, Bases, Bases%, Genome%, Size
TGGGCTCGGTTA, 6, 100.0, 72, 100.0, 0.0, 12
# ANALYSIS END Fri Mar 8 02:28:54 2019
```



## 参考来源

https://www.future-science.com/doi/full/10.2144/btn-2018-0057#

https://bioserf.org/r/p/swebserf

<hr />