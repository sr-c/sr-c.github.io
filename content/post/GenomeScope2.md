---
title: "使用GenomeScope2评估基因组"
date: 2020-09-11T09:35:51+08:00
tags: []
categories: [Genomics, Assembly]
draft: false
---

GenomeScope是基因组评估的有效工具，但是之前只能够预测二倍体基因组，现在使用GenomeScope2则可以预测多倍体（至多六倍体）及其倍性。

<!-- more -->

## 安装

```bash
git clone https://github.com/tbenavi1/genomescope2.0.git
cd genomescope2.0/
Rscript install.R
```

注意，在安装前需要确认当前用户对目前环境中R的libraries安装路径存在写入权限。

```R
local_lib_path = "~/R_libs/"

install.packages('minpack.lm', lib=local_lib_path)
install.packages('argparse', lib=local_lib_path)
install.packages('.', repos=NULL, type="source", lib=local_lib_path)
```

如此，则应该修改其中的`local_lib_path`为当前可用的`R_LIBS`路径。

对于大多数用户，如果没有写入权限，那么可以直接新建目录`~/R_libs`，并在`Renviron`中声明

```bash
mkdir ~/R_libs
echo "R_LIBS=~/R_libs/" >> ~/.Renviron
```

## 使用

首先，使用KMC或jellyfish获取`histogram_file`

### 获取k-mer的频率直方图

#### KMC

```bash
mkdir tmp
ls *.fastq > FILES
##-k设定k-mer长度，-t设定使用线程数，-m设定使用内存大小，-ci设定使用k-mer count的下界，-cs设定使用k-mer count的上界。
kmc -k21 -t10 -m64 -ci1 -cs10000 @FILES reads tmp/

kmc_tools transform reads histogram reads.histo -cx10000
```

#### jellyfish

```bash
jellyfish count -C -m 21 -s 1000000000 -t 10 *.fastq -o reads.jf
jellyfish histo -t 10 reads.jf > reads.histo
```

### 命令行运行GenomeScope

将`genomescope2.0`加入环境变量后，即可直接运行

```bash
genomescope.R -i histogram_file -o output_dir -k k-mer_length
```

### 网页运行GenomeScope

将`histogram_file`上传至[在线服务](http://genomescope.org/genomescope2.0)，即可。

## 参考来源

https://bioinformaticsworkbook.org/dataAnalysis/GenomeAssembly/genomescope.html

