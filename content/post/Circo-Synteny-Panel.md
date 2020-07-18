---
title: Circos共线性比较
tags: [Circos, Mummer]
date: 2020-06-03 21:37:09
permalink:
categories: [Genomics]
description:
---
<p class="description">绘制两个基因组之间匹配关系的Circos圈图</p>

<!-- more -->

![效果示意图](http://xuzhougeng.top/upload/2019/7/circos-link-2-2222e6d11bd049c0b5e9a200c5e50508.png)

# 数据准备

## 获取区域关联关系

### Mummer匹配

获取已硬屏蔽了重复序列的基因组序列，使用nucmer(Mummer)对其进行匹配

```bash
#比对
nucmer --prefix=ref_qry ref.fasta qry.fasta 
#过滤，除去不太适合的部分，但结果不适合读
delta-filter -q ref_qry.delta > ref_qry.filter 
#将结果转换为以人类可读的格式显示匹配的坐标
show-coords -rcl ref_qry.delta > ref_qry.coords 

##Genome Linkage info
##以一定标准（如匹配区域长度>2 kb, 相似度>80%）过滤匹配的区域
awk '$5>50000, OFS="\t"{print $12,$1,$2,$13,$3,$4}' ../../Mummer/out.1coords > req_qry.50k.LinkedRegion.info
```

## 基因组核型（染色体长度）

```bash
seqkit fx2tab ~/reference/genome/ref.fasta -l -n > ref.SeqLen
seqkit fx2tab ~/reference/genome/qry.fasta -l -n > qry.SeqLen
cat ref.SeqLen qry.SeqLen > all.SeqLen

awk '{print "chr\t-\t"$1"\t"$1"\t"0"\t"$2"\tblack"}' ref.SeqLen > karyotype.ref.txt
awk '{print "chr\t-\t"$1"\t"$1"\t"0"\t"$2"\tblack"}' qry.SeqLen > karyotype.qry.txt
cat karyotype.ref.txt karyotype.qry.txt > karyotype.both.txt
```

## 基因/重复序列密度

```bash
## Gene position
awk '$3=="gene", OFS="\t"{print $1,$4,$5,1,$7}' ~/reference/annotation/ref.gff3 > ref.genePos
awk '$3=="gene", OFS="\t"{print $1,$4,$5,1,$7}' ~/reference/annotation/qry.gff3 > qry.genePos
cat ref.genePos qry.genePos > All.GenePos

## Repeat Region position
awk 'OFS="\t"{print $1,$4,$5,1,$7}' RepeatMasker/ref.fasta.out.gff > ref.RepPos
awk 'OFS="\t"{print $1,$4,$5,1,$7}' RepeatMasker/qry.fasta.out.gff > qry.RepPos
cat ref.RepPos qry.RepPos | grep -v "#" > All.RepPos

## 设定滑动窗口
bedtools makewindows -g Os.AllContigLen -w 100000 > both.windows
## 计算基因/重复序列单元的覆盖度
bedtools coverage -a All.GenePos -b both.windows | cut -f 1-4 > genes_num.txt
bedtools coverage -a All.RepPos -b both.windows| cut -f 1-4 > reps_num.txt
```

## GC含量

```bash
## 临时合并ref与qry的基因组序列，方便同时计算
cat ~/reference/genome/ref.fasta ~/reference/genome/qry.fasta > tmp.fasta
## 设定滑动窗口
bedtools makewindows -g All.SeqLen -w 100000 > both.windows
## 使用bedtools nuc计算GC含量，默认输出的第5列为GC含量
bedtools nuc -fi tmp.fasta -bed both.windows | cut -f 1-3,5 | sed '1d' > both.GCrate.txt
## 删除临时文件
rm tmp.fasta tmp.fasta.fai
```

## 环境配置

```bash
# 安装circos
conda create -c bioconda -n circos circos
# 测试circos
conda activate circos
# 确认安装
circos -V
# 输出如下
# circos | v 0.69-8 | 15 Jun 2019 | Perl 5.026002
```

# Circos绘图

## circos.conf

```
karyotype = karyotype.ref.txt,karyotype.qry.txt

## 设定显示的染色体
chromosomes_display_default = no
chromosomes = refContig01;refContig02;refContig03;refContig04;refContig05;refContig06;refContig07;refContig08;refContig09;refContig10;refContig11;refContig12;refContig13;refContig14;refContig15;refContig16;qryContig01;qryContig02;qryContig03;qryContig04;qryContig05;qryContig06;qryContig07;qryContig08;qryContig09;qryContig10;qryContig11;qryContig12;qryContig13;qryContig14;qryContig15;qryContig16;qryContig17;qryContig18
## 设定染色体的排列顺序
chromosomes_order = qryContig18,qryContig17,qryContig16,qryContig15,qryContig14,qryContig13,qryContig12,qryContig11,qryContig10,qryContig09,qryContig08,qryContig07,qryContig06,qryContig05,qryContig04,qryContig03,qryContig02,qryContig01

## 逆转qry序列的方向（从最大值向零沿逆时针反向排列），正则匹配符合的染色体
chromosomes_reverse = /qryContig/
## 设定染色体颜色
chromosomes_color = /ref/=pastel1-3-qual-1,/qry/=pastel1-3-qual-2
## 此设定会ref与qry的染色体各占半圈，但每个染色体的长度都被缩放至同一长度
# chromosomes_scale = /refContig/:0.5rn;/qryContig/:0.5rn

## 设定染色体上的标尺
<<include ticks.conf>>

<ideogram>
<spacing>
## 染色体之间默认的间距
default = 1u
## 设定ref与qry之间的间隙稍大于一般值
  <pairwise refContig01 qryContig01>
   spacing = 4u
  </pairwise>
  
  <pairwise refContig16 qryContig18>
   spacing = 4u
  </pairwise>
</spacing>

radius           = 0.90r
thickness        = 20p
fill             = yes
stroke_color     = dgrey
stroke_thickness = 2p
show_label     = yes #展示label
label_font     = default # 字体
label_radius   = dims(ideogram,radius) + 0.08r #位置
label_size     = 16 # 字体大小
label_parallel = no # 是否平行

label_format   = eval(sprintf("%s",var(chr))) # 格式
</ideogram>

<plots>

<plot>
## 基因密度
type      = line
thickness = 1
max_gap = 1u
file = genes_num.txt
color   = red
r0      = 0.86r
r1      = 0.95r
</plot>

## 重复序列密度
<plot>
type      = line
thickness = 1
max_gap = 1u
file = reps_num.txt
color   = blue
r0      = 0.76r
r1      = 0.85r
</plot>

## GC含量
<plot>
type    = heatmap
file    = both.GCrate.txt
color   = ylorrd-9-seq
r1      = 0.95r
r0      = 0.99r
</plot>

</plots>

## 设定染色体之间的关联信息
<<include ./etc/links.conf>>

<image>
angle_offset* = -88 ## 选项后加*表示覆盖已有的设置
dir*    = .    # 输出文件夹
radius* = 500p # 图片半径，默认为1500p
svg*    = yes   # 是否输出svg
<<include etc/image.conf>>
</image>

<<include etc/colors_fonts_patterns.conf>>
<<include etc/housekeeping.conf>>
```

### links.conf

```
<links>
<link>
file          = req_qry.50k.LinkedRegion.info
radius        = 0.75r
color         = greys-9-seq-3
ribbon = yes

<rules>
<rule>
condition = var(chr1) eq "refContig01"
color=spectral-9-div-1
</rule>
<rule>
condition = var(chr1) eq "refContig02"
color=spectral-11-div-3
</rule>
<rule>
condition = var(chr1) eq "refContig03"
color=spectral-11-div-5
</rule>
<rule>
condition = var(chr1) eq "refContig04"
color=spectral-11-div-6
</rule>
<rule>
condition = var(chr1) eq "refContig05"
color=spectral-11-div-8
</rule>
<rule>
condition = var(chr1) eq "refContig06"
color=spectral-11-div-9
</rule>
<rule>
condition = var(chr1) eq "refContig07"
color=spectral-11-div-10
</rule>
<rule>
condition = var(chr1) eq "refContig08"
color=spectral-11-div-11
</rule>
<rule>
condition = var(chr1) eq "refContig09"
color=blue_a4
</rule>

</rules>

</link>
</links>
```

###  ticks.conf

```
chromosomes_units = 1000000
show_ticks          = yes
show_tick_labels    = yes

<ticks>

radius           = 1r
color            = black
thickness        = 2p

multiplier       = 1e-6 #输出的标签为实际长度与其相乘

format           = %d # %d表示显示整数

<tick>
spacing        = 1u
size           = 5p
</tick>

<tick>
spacing        = 5u
size           = 10p
show_label     = yes
label_size     = 10p
label_offset   = 10p
format         = %d
</tick>

</ticks>
```

## 单位

> 上面出现了控制图形不同元素大小的三个单位，p,r,u。p(pixels), 表示绝对大小， r(relative), 相对大小， u(chromosome unit)。 如果使用p作为单位，需要考虑最终输出图形`<image>`定义的radius。 而r是相对大小，会随着最终图形大小而发生变换。u一般在显示刻度时使用。

## 颜色

Circos中颜色的命名格式为`PALETTE-NUMCOLORS-TYPE-IDX`:

- PALETTE:调色版名，如rdylbu
- NUMCOLORS: 颜色数目,  11
- 调色版类型: div(diverging), seq(sequential), qual(qualitative)
- IDX: 调色版中的颜色索引

Circos的颜色设置来自https://colorbrewer2.org/。因此，`gnbu-9-seq`对应的是就是下图的`9-class GnBu`。因此，`gnbu-9-seq`对应的是就是下图的`9-class GnBu`。

![颜色设置](http://xuzhougeng.top/upload/2019/7/circos-colors-88bba063fa264c9f9825a9b9fd3baad0.png)

## 参考来源

http://xuzhougeng.top/tags/circos

https://www.royfrancis.com/beautiful-circos-plots-in-r/

http://xuzhougeng.top/archives/Using-MUMmer-to-align-genome

http://xuzhougeng.top/archives/Visualization-of-MUMMER-Result

<hr />