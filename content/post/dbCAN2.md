---
title: 使用dbCAN2进行CAZyme注释
tags: [CAZyme,dbCAN,annotation]
date: 2018-10-12 18:28:38
permalink:
categories:
description:
---
<p class="description">使用dbCAN2 meta server进行CAZyme注释</p>

<!-- more -->

[dbCAN2 meta server](http://cys.bios.niu.edu/dbCAN2/index.php)是[dbCAN web server](http://csbl.bmb.uga.edu/dbCAN/)的升级版本，使用HMMER, DIAMOND与Hotpep三种方式进行比对，并且允许提交核算序列（原核生物）。2018年8月25日，dbCAN 数据库升级至7.0版本。

## 数据准备

输入的序列应当为FASTA格式。对于原核生物，dbCAN2支持直接输入核酸序列（便于宏基因组数据输入）；对于真核生物，用户还是应当自行预测出蛋白序列作为输入。

使用CGCFinder预测CAZyme 基因簇(CAZyme gene clusters, CGCs)，需要提供基因的位置信息（BED/GFF格式）。其要求的[示例](http://cys.bios.niu.edu/dbCAN2/static/images/bed.txt)格式如下。

>Scaffold1	prot_00001	3174	3575	+
>Scaffold1	prot_00002	6297	7073	-
>Scaffold1	prot_00003	7066	8793	-
>Scaffold1	prot_00004	8793	10658	-
>Scaffold1	prot_00005	10782	10907	-
>Scaffold1	prot_00006	11206	11871	-

如提交GFF文件，其中包含'CDS'字符的列才会被识别。基因ID应当包含在`Name`或`ID`标签中。

## 结果输出

结果页首先会展示3种比对工具注释到的CAZyme，整合3种方法的结果，提高准确率。

若提供了基因位置信息，结果中还有CGCFinder预测出的CAZyme基因簇。结果页中还可调整CGCFinder的阈值，方便筛选预测结果。

## 参考来源

http://cys.bios.niu.edu/dbCAN2/help.php

<hr />