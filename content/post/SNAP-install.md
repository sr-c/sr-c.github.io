---
title: SNAP软件安装
tags: [SNAP,ab initio]
date: 2018-09-02 18:37:52
permalink:
categories:
description:
---
<p class="description">SNAP是用于基因组从头预测的软件，虽然效果不如augustus，但也有应用的场景。</p>

<!-- more -->

## 软件下载

虽然在Korf Lab的官网上提供[最新版本](http://korflab.ucdavis.edu/Software/snap-2013-11-29.tar.gz)(latest release 11/29/2013)的下载，但此版本在本机(CENTOS 6.9, GCC )编译时出现错误，改而从github站点下载，成功完成编译。

```bash
git clone https://github.com/KorfLab/SNAP.git
cd SNAP
make -j 4
echo 'PATH=$PATH:~/opt/SNAP' >> ~/.bashrc
source ~/.bashrc
```

SNAP Training 需要旧版本EVM提供的一支程序将GFF3文件转换为ZFF格式。

```bash
cd ~/software
wget https://ayera.dl.sourceforge.net/project/evidencemodeler/OLD_CONTENT/evidencemodeler/EVM_r2012-06-25.tgz
tar zxf EVM_r2012-06-25.tgz -C ~/opt/
```

## SNAP Training

转换ZFF文件

```bash
export PERL5LIB=$PERL5LIB:~/opt/EVM_r2012-06-25/PerlLib/
~/opt/EVM_r2012-06-25/OtherGeneFinderTrainingGuide/SNAP/gff3_to_SNAP_train.pl species.gff3 species_genome.fasta
```

## 参考来源

https://github.com/KorfLab/SNAP

<hr />