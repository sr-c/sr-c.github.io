---
title: 真菌RIP分析
tags: [ripcal,RIP]
date: 2018-10-18 20:39:02
permalink:
categories:
description:
---
<p class="description"> Repeat-induced point mutation (RIP)发生于某些特定种类真菌的重复DNA序列中。使用RIPCAL计算真菌的RIP变异。</p>

<!-- more -->

## 安装

RIPCAL最新版应为2.0版本，但其软件主页默认下载项却仍为1.0.4版本，应当注意下载时的选择。

```bash
wget http://nchc.dl.sourceforge.net/project/ripcal/RIPCAL/RIPCAL_2.0/ripcal2_install.zip
unzip ripcal2_install.zip
mkdir ~/opt/ripcal2/
mv ripcal2_intall/perl/* ~/opt/ripcal2/
chmod 755 ~/opt/ripcal2/*
mv ripcal2_install/RIPCAL_manual_v1_0.pdf ~/opt/ripcal2/
echo 'PATH=$PATH:~/opt/ripcal2/' >> ~/.bashrc
source ~/.bashrc

#安装需求的模块
cpanm Math::Round Tk
```

## 配置

`RIPCAL`默认调用`clustalw`进行多序列比对，若目前使用的是`clustalw2`，则需要做相应的修改。

```bash
vi ~/opt/ripcal2/ripcal
# 将system()括号内的clustalw修改为clustalw2
```

## 输入文件

### FASTA

RIPCAL接受标准的FASTA/Pearson序列格式，或FASTA[多序列比对格式](https://biopython.org/wiki/Multiple_Alignment_Format)。

### GFF

在分析多于一个重复序列家族时，RIPCAL要求输入GFF文件。输入的GFF文件第1列为序列ID，第3列为`Repeat_Region`，第9列中以`Target`标签标注重复序列的种类。

## 运行

```bash
#Alignment-based mode
ripcal -c --type align --seq input.fasta --gff input.gff --model consensus
ripcal_summarise *_RIPALIGN.TXT > outputfile
#Di-nucleotide mode
ripcal -c -t index -s input.fasta -g input.gff
# Index scan mode
ripcal -c -t scan -s input.fasta
```



## 参考来源

https://sourceforge.net/projects/ripcal/files/RIPCAL/RIPCAL_2.0/RIPCAL_manual_v1_0.pdf

<hr />