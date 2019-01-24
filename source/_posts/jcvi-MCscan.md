---
title: 使用jcvi进行MCscan可视化
tags: [jcvi, MCscan]
date: 2019-01-11 19:19:34
permalink:
categories:
description:
---
<p class="description">jcvi是Haibao Tang开发的基因组组装注释与比较基因组分析工具，可视化效果很不错</p>

<!-- more -->

## 安装

软件推荐使用`pip`安装，但是作为普通用户，还是选择使用conda安装

```bash
conda create -n jcvi python=2.7
source activate jcvi
# 安装前需要确认存在bioconda和r channel，安装前先更新conda
conda --add-channel r
conda install jcvi last
```

后续还可能会由于缺少`scipy`在绘图时报错`ImportError: No module named scipy.spatial`，因此可提前安装

```bash
# 安装scipy
#conda-forge scipy-1.2.0
conda install scipy
```

## 准备数据

`jcvi`支持直接从[phytozome](https://phytozome.jgi.doe.gov/pz/portal.html)下载数据

```bash
python -m jcvi.apps.fetch phytozome
```

按照名称查看

```bash
python -m jcvi.apps.fetch phytozome
...
         Acoerulea               Alyrata             Athaliana
       Bdistachyon                 Brapa           Cclementina
           Cpapaya          Creinhardtii              Crubella
          Csativus             Csinensis Csubellipsoidea_C-169
          Egrandis                Fvesca                  Gmax
        Graimondii        Lusitatissimum            Mdomestica
        Mesculenta             Mguttatus     Mpusilla_CCMP1545
   Mpusilla_RCC299           Mtruncatula          Olucimarinus
           Osativa               Ppatens              Ppersica
      Ptrichocarpa             Pvirgatum             Pvulgaris
         Rcommunis              Sbicolor              Sitalica
     Slycopersicum       Smoellendorffii            Stuberosum
            Tcacao            Thalophila              Vcarteri
         Vvinifera                 Zmays         early_release
```

常见的植物物种都可以直接下载

```bash
# download sequences and coordinates of grape and peach
python -m jcvi.apps.fetch phytozome Vvinifera,Ppersica
# convert the GFF to BED file
python -m jcvi.formats.gff bed --type=mRNA --key=Name Vvinifera_145_gene.gff3.gz -o grape.bed
python -m jcvi.formats.gff bed --type=mRNA --key=Name Ppersica_139_gene.gff3.gz -o peach.bed
# clean headers to remove description fiedls from Phytozome FASTA files.
python -m jcvi.formats.fasta format --sep="|" Vvinifera_145_cds.fa.gz grape.cds
python -m jcvi.formats.fasta format --sep="|" Ppersica_139_cds.fa.gz peach.cds
```

## 共线性分析

```python
python -m jcvi.compara.catalog ortholog grape peach
ls grape.peach.*
grape.peach.lifted.anchors  grape.peach.anchors  grape.peach.last.filtered grape.peach.last grape.peach.pdf
```

程序默认按照可用线程，调用全部进行LAST比对，速度很快。

## 可视化

### 点阵图

目前版本`0.8.12`在共线性分析后，自动绘制点阵图，手动运行方式如下

```bash
python -m jcvi.graphics.dotplot grape.peach.anchors
```

在两者的点阵图中，不论水平方向或者竖直方向查看，都可以发现不超过3次的共线性区域。这说明，🍇和🍑之间发生了基因组三倍体化的事件，使得出现了这样3:3的模式。

如果仔细观察，还可以发现3个共线性区域中常常有一个信号更强，对应着两个基因组之间的直系同源区域。如果我们只想要得到这些1:1直系同源的区域呢？我们只需要重复之前的比对，同时加上选项`--cscore=.99`即可。C-score是由LAST比对区域到BLAST比对区域的比值确定。(C-score is defined by the ratio of LAST hit to the best BLAST hits to either the query and hit. )0.99的C-score阈值有效地过滤LAST比对结果，从而得到最佳相互比对结果(reciprocal best hit, RBH)。

```bash
rm grape.peach.last.filtered 
python -m jcvi.compara.catalog ortholog grape peach --cscore=.99
python -m jcvi.graphics.dotplot grape.peach.anchors
```

![Grape-peach-synteny-.99](https://camo.githubusercontent.com/222430d386faca2179528563cde9d8339bc8ad72/68747470733a2f2f7777772e64726f70626f782e636f6d2f732f33326d6d6f306b66687478356238672f67726170652e70656163682e6373636f72652e39392e706e673f7261773d31)

还可以手动查看synteny depth分布

```bash
python -m jcvi.compara.synteny depth --histogram grape.peach.anchors
```

![Grape-peach-depth](https://camo.githubusercontent.com/d82b2f2ed6b675ab090564d74d9b78ac2b6e21c9/68747470733a2f2f7777772e64726f70626f782e636f6d2f732f686878326474727972756d3267796f2f67726170652e70656163682e64657074682e706e673f7261773d31)

### 共线型图

同样由`grape.peach.anchors`生成，除了BED和共线性文件，还需要手动设置两个文件用于绘图

1. `seqids`设定绘制哪些染色体，一般会去掉较小和未定位的scaffolds，下方设定了19个🍇的染色体与8个🍑的染色体

   ```
   chr1,chr2,chr3,chr4,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr16,chr17,chr18,chr19
   scaffold_1,scaffold_2,scaffold_3,scaffold_4,scaffold_5,scaffold_6,scaffold_7,scaffold_8
   ```

2. `layout`设定画板上如何绘制。整体上canvas绘图的x,y轴的定位在0-1之间，前三行设定了染色体轨道的位置，循环，颜色，标签，垂直排列以及对应的BED文件。下方的track0就是🍇，track1就是🍑。下一节就是设定在哪些tracks之间绘制共线性关系。`e,0,1`代表着绘制track0与track1之间的共线性，使用`grape.peach.anchors.simple`文件中的详细信息。

   ```
   # y, xstart, xend, rotation, color, label, va,  bed
    .6,     .1,    .8,       0,      , Grape, top, grape.bed
    .4,     .1,    .8,       0,      , Peach, top, peach.bed
   # edges
   e, 0, 1, grape.peach.anchors.simple
   ```

注意，这两个设定文件末尾不能够存在多余的空行，否则会报错[#56](https://github.com/tanghaibao/jcvi/issues/56)

```bash
LayoutLine' object has no attribute 'color'
```

3. 从`.anchors`文件中产生更简要的`.simple`文件

```bash
python -m jcvi.compara.synteny screen --minspan=30 --simple grape.peach.anchors grape.peach.anchors.new 
```

绘图

```bash
python -m jcvi.graphics.karyotype seqids layout
```

![Grape-peach-karyotype](https://camo.githubusercontent.com/b43d5bd13f2abaf1255fac49d6fe8ba5b3f86ec5/68747470733a2f2f7777772e64726f70626f782e636f6d2f732f35316b386a756a797a616765336f612f67726170652e70656163682e6b6172796f747970652e706e673f7261773d31)

### 局部可视化

首先从基因水平提取匹配的区块

```bash
python -m jcvi.compara.synteny mcscan grape.bed grape.peach.lifted.anchors --iter=1 -o grape.peach.i1.blocks
```

参数`--iter=1`表明从每个🍇区域中提取1个最佳匹配区域。若将`--iter`设置为2，那么每个🍇区域就会有2个对应的🍑区域。这一点对于基因组重复区域的绘制很有帮助。

目前`grape.peach.i1.blocks`中包含有很多局部区域，我们可以手动选择需要绘制的部分。

```bash
head -50 grape.peach.i1.blocks > blocks
```

最终，我们仍然需要一个`layout`文件进行绘图，`blocks.layout`文件如下

```
# x,   y, rotation,   ha,     va,   color, ratio,            label
0.5, 0.6,        0, left, center,       m,     1,       grape Chr1
0.5, 0.4,        0, left, center, #fc8d62,     1, peach scaffold_1
# edges
e, 0, 1
```

然后，我们就可以进行绘制了

```bash
cat grape.bed peach.bed > grape_peach.bed
python -m jcvi.graphics.synteny blocks grape_peach.bed blocks.layout
```

![Grape-peach-blocks](https://camo.githubusercontent.com/be5a68f92e48eaf6173442ce8543cefd89445e2b/68747470733a2f2f7777772e64726f70626f782e636f6d2f732f6978326d736464736e6477653276652f67726170652e70656163682e626c6f636b732e706e673f7261773d31)

## 参考来源

https://github.com/tanghaibao/jcvi/wiki/MCscan-%28Python-version%29

https://www.jianshu.com/p/39448b970287

<hr />