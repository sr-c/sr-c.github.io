---
title: 使用BUSCO评价基因组组装质量
tags: [assembly]
date: 2018-06-04 22:48:08
permalink:
categories:
description:
image:
---
<p class="description">利用相近物种的保守基因序列，BUSCO被用于评价参考基因组的组装质量。</p>

<!-- more -->

# 软件安装

BUSCO的手动安装并不困难，但是BUSCO依赖`tblastn`与`augustus`，而augustus的安装比较复杂，因此选择使用conda自动化安装应当是首选。conda的安装参照后文。
```bash
conda install busco
```

## 下载数据库
```bash
cd ~/database/busco
wget http://busco.ezlab.org/v2/datasets/euarchontoglires_odb9.tar.gz
tar -zxvf eukaryota_odb9.tar.gz
```
# 软件使用

```bash
run_BUSCO.py -i yourassemble.fa -o yourassemble_busco -m genome -l ~/database/busco_database/hymenoptera_odb9/ -c 8 
```

参数说明：

```
-i|--in: 输入文件
-o|--out: 输出文件、文件夹前缀
-l|--lineage: 数据库的路径
-m|--mode: 运行模式，geno|tran|prot
-c|--cpu: 线程数，默认1.
```

# 结果解读

BUSCO 的结果一看就懂，主要看统计文件run_busco/short_summary_busco.txt 。我们的测试数据数据量太少，下面放一个真实项目的数据。

```
$ cat run_busco/short_summary_busco.txt 
# 省略若干行
# Summarized benchmarking in BUSCO notation for file longest_isoform.fasta
# BUSCO was run in mode: tran

    C:89.0%[S:49.9%,D:39.2%],F:4.4%,M:6.5%,n:429

    382    Complete BUSCOs (C)
    214    Complete and single-copy BUSCOs (S)
    168    Complete and duplicated BUSCOs (D)
    19    Fragmented BUSCOs (F)
    28    Missing BUSCOs (M)
    429    Total BUSCO groups searched
```

真实项目中，Complete BUSCOs (C) 的比例通畅都能达到 80% 以上。不过如果低于这个值，也未必有问题，还是要根据实际项目情况判断。除了跟经验值比较。**更有意义的是使用不同软件、参数多组装几个版本，挑选最优版。**
## 可视化
如果有多个版本，就可以画这样一张图，分析哪个版本更优。

```
mkdir my_summaries 
#拷贝多个版本的结果到同一个目录中，注意重命名 
cp run_SPEC1/short_summary_SPEC1.txt my_summaries/ 
cp run_SPEC2/short_summary_SPEC2.txt my_summaries/ 
cp run_SPEC3/short_summary_SPEC3.txt my_summaries/ 
cp run_SPEC4/short_summary_SPEC4.txt my_summaries/ 
cp run_SPEC5/short_summary_SPEC5.txt my_summaries/ 
python BUSCO_plot.py –wd my_summaries评价质量可以使用BUSCO自带的脚本用R作图，反映short_report中的内容，按照预测出基因的比例进行比较。
```

参考来源：
http://genek.tv/article/29
<hr />
