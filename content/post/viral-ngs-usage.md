---
title: "Viral Ngs Usage"
date: 2020-09-01T17:09:39+08:00
tags: []
categories: [Genomics]
draft: true
---

viral-ngs是Broad institute出品的病毒基因组工具。它能够分析病毒的（宏）基因组NGS数据。

<!-- more -->

## 安装

### Docker

默认的Docker镜像放置于quay.io.

```bash
docker pull quay.io/broadinstitute/viral-ngs
```

### Conda

目前，viral-ngs的版本为1.25.0，按照官方推荐的conda安装方式

```bash
#update conda
conda update -y conda
#add channels
conda config --add channels r
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
conda config --add channels broad-viral
#install in a new enviroment
conda create -n viral-ngs-env viral-ngs
```

但是，这个解析过程非常长（过了一夜仍然没有跑完）。

于是我尝试使用比conda更快的mamba来解决。

```bash
conda create -n viral-ngs-env
source activate viral-ngs-env
conda install mamba -c conda-forge
mamba install -c broad-viral viral-ngs
```

但是，会遇到如下问题

```bash
Looking for: ['viral-ngs']

Encountered problems while solving.
Problem: package viral-ngs-1.19.3-py36pl522hdeea2a2_0 requires python >=3.6,<3.7.0a0, but none of the providers can be installed
```

似乎是目前的python版本不合适`python --version`查看一下，目前版本是`3.8.5`，于是`conda install python=3.6`. 再行尝试，又得到如下报错

```
Problem: package matplotlib-1.5.3-np111py36_1 requires pyqt 5.*, but none of the providers can be installed
```

于是`conda install -c anaconda pyqt`, 但是得到一堆软件冲突信息，于是改为`mamba install -c anaconda pyqt`可正常安装，再尝试`mamba install -c broad-viral viral-ngs`, 得到

```
Problem: package qt-5.9.7-h52cfd70_2 requires freetype >=2.9.1,<3.0a0, but none of the providers can be installed
```

尝试将broad-viral频道加入到`~/.condarc`中，并保持最高优先级。再次尝试`mamba install viral-ngs`，得到

```bash
Problem: nothing provides openssl >=1.1.1,<1.1.2.0a0 needed by python-2.7.15-h9bab390_2
```

但是，当前环境存在的openssl版本为`1.1.1g`, 该问题目前未找到完善的解决方案。

通过`mamba repoquery search viral-ngs`，得到如下版本信息

>Name      Version Build               Channel
>────────────────────────────────────────────────────────────
> viral-ngs 1.25.0  py36pl526h14c3975_0 broad-viral/linux-64
> viral-ngs 1.24.0  py36pl526h14c3975_0 broad-viral/linux-64
> viral-ngs 1.23.0  py36pl526_0         broad-viral/linux-64
> viral-ngs 1.22.1  py36pl522h34ae550_0 broad-viral/linux-64
> viral-ngs 1.22.0  py36pl522hc0e9449_0 broad-viral/linux-64
> viral-ngs 1.21.2  py36pl522h1cc5362_0 broad-viral/linux-64
> viral-ngs 1.21.1  py36pl522h885d58e_0 broad-viral/linux-64
> viral-ngs 1.21.0  py36pl522he4af088_0 broad-viral/linux-64
> viral-ngs 1.20.1  py36pl522hf58017e_0 broad-viral/linux-64
> viral-ngs 1.20.0  py36pl522h70fead5_0 broad-viral/linux-64
> viral-ngs 1.19.3  py36pl522hdeea2a2_0 broad-viral/linux-64
> viral-ngs 1.19.2  py36pl522hdf56793_0 broad-viral/linux-64
> viral-ngs 1.19.1  py36pl522h5147ba1_0 broad-viral/linux-64
> viral-ngs 1.19.0  py36pl522h57b9dcf_0 broad-viral/linux-64
> viral-ngs 1.18.2  py36pl522h9c00eff_0 broad-viral/linux-64
> viral-ngs 1.18.1  py27pl522h3f3c600_0 broad-viral/linux-64
> viral-ngs 1.18.1  py36pl522h0f2ed20_0 broad-viral/linux-64
> viral-ngs 1.18.0  py27pl522h20547ac_0 broad-viral/linux-64
> viral-ngs 1.18.0  py36pl522hc00acc0_0 broad-viral/linux-64
> viral-ngs 1.18.0  py36pl522h2cb8805_0 broad-viral/linux-64
> viral-ngs 1.18.0  py27pl522h72a095d_0 broad-viral/linux-64
> viral-ngs 1.17.3  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.3  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.3  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.2  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.2  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.2  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.1  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.1  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.1  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.0  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.0  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.17.0  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.16.0  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.16.0  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.16.0  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.4  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.4  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.4  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.3  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.3  py35pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.3  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.2  py27pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.2  py34pl5.20.3_0      broad-viral/linux-64
> viral-ngs 1.15.2  py35pl5.20.3_0      broad-viral/linux-64

可以发现，在`1.18.1`版本之前，viral-ngs支持`py36`与`py27`, 之后放弃了对`python2`的支持。于是我们尝试安装python2环境的`1.18.1`版本。

考虑安装较早版本的`viral-ngs`

```bash
conda create -n viral-ngs-py27 python=2.7
conda activate viral-ngs-py27
(viral-ngs-py27) [shurh@cluster ~]$ conda install mamba -c conda-forge
(viral-ngs-py27) [shurh@cluster ~]$ mamba install viral-ngs=1.18.1
```

即可成功安装`1.18.1`版本的`viral-ngs`

如果得到如下报错，请删除`~/.condarc`中的`channel_priority=strict`

> Cannot use strict priority with mamba!

#### 下载GATK3

由于软件协议，GATK需要我们手动下载安装。

```bash
source activate viral-ngs-py27
mkdir -p /path/to/gatk_dir
wget -O - 'https://software.broadinstitute.org/gatk/download/auth?package=GATK-archive&version=3.6-0-g89b7209' | tar -xjvC /path/to/gatk_dir
gatk3-register /path/to/gatk_dir/GenomeAnalysisTK.jar
```

> 注：其中GATK3的[下载地址](https://console.cloud.google.com/storage/browser/gatk-software/package-archive/gatk)现在已变更至Google Cloud, 在conda/mamba安装时，注意当前需要的GAKE版本。目前我安装的`1.18.1-py27pl522h3f3c600_0 `版本`viral-ngs`需要`3.6.0`版本的GATK, 下载对应的版本即可。

### 快速安装脚本

```bash
wget https://raw.githubusercontent.com/broadinstitute/viral-ngs/master/easy-deploy-script/easy-deploy-viral-ngs.sh && chmod a+x ./easy-deploy-viral-ngs.sh && ./easy-deploy-viral-ngs.sh setup
```

该脚本要求环境中存在python3，且并非conda环境。

## 使用

### 数据预处理

默认的流程中使用picard.samTofastq将bam文件中的序列转化为fastq，再使用trimmomatic过滤数据，然后使用[prinseq](http://prinseq.sourceforge.net/manual.html)的rmdup_fastq_paired去除PCR重复，再使用picard.sam.FastqToSam将序列转化为bam文件

```bash
source activate viral-ngs-py27
##n_reads设置输出reads的数量，默认值为1000000. 设置超过输入的read数，以跳过subsamp
assembly.py trim_rmdup_subsamp ../LymaNPV.bam ~/opt/Trimmomatic-0.38/adapters/TruSeq3-PE-2.fa LymaNPV.trim_rmdup.bam --n_reads 10000000
assembly.py assembly.py trim_rmdup_subsamp ../LymaNPV.bam ~/opt/Trimmomatic-0.38/adapters/TruSeq3-PE-2.fa LymaNPV.trim_rmdup_subsamp.bam --n_reads 1000000
samtools sort -n LymaNPV.trim_rmdup.bam | samtools fastq LymaNPV.trim_rmdup.bam -1 LymaNPV.trim_rmdup.1.fastq -2 LymaNPV.trim_rmdup.2.fastq -s LymaNPV.singleton.fastq
```

其中`prinseq`的输出如下

>Input and filter stats:
>        Input sequences (file 1): 4,666,236
>        Input bases (file 1): 666,391,075
>        Input mean length (file 1): 142.81
>        Input sequences (file 2): 4,666,236
>        Input bases (file 2): 657,157,533
>        Input mean length (file 2): 140.83
>        Good sequences (pairs): 4,119,910
>        Good bases (pairs): 1,159,944,732
>        Good mean length (pairs): 281.55
>        Good sequences (singletons file 1): 93 (0.00%)
>        Good bases (singletons file 1): 12,090
>        Good mean length (singletons file 1): 130.00
>        Good sequences (singletons file 2): 45 (0.00%)
>        Good bases (singletons file 2): 3,341
>        Good mean length (singletons file 2): 74.24
>        Bad sequences (file 1): 546,233 (11.71%)
>        Bad bases (file 1): 81,757,665
>        Bad mean length (file 1): 149.68
>        Bad sequences (file 2): 93 (0.00%)
>        Bad bases (file 2): 9,697
>        Bad mean length (file 2): 104.27
>        Sequences filtered by specified parameters:
>        ns_max_n: 337
>        derep: 546108

## 参考来源

http://viral-ngs.readthedocs.org/

https://mp.weixin.qq.com/s/45QsM82tUU6d2M5_UwQLsw

http://prinseq.sourceforge.net/manual.html

