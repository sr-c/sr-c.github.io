---
title: 【debug】maker3
tags: [maker, debug, annotation]
date: 2018-11-12 11:01:57
permalink:
categories:
description:
---
<p class="description">maker3运行部分scaffold报错</p>

<!-- more -->

## 问题描述

### BUG1

运行maker3.01.02时，部分scaffold运行失败，其报错信息如下

```bash
substr outside of string at ../lib/PhatHit_utils.pm line 850.
```

本机perl版本为5.10.1，经过查询，问题似乎能够通过较高版本的perl解决

### BUG2

使用tRNAcscan-SE-2.0.0版本用于检测tRNA时，maker默认调用的`-p`参数在新版本中并不支持，如此API的改变使得maker程序停滞在tRNAscan阶段，程序无法进行。

## 解决方案

### 使用perlbrew安装5.16.3版本perl

```bash
perlbrew install 5.16.3 --thread --multi
perlbrew use 5.16.3
```

### 重新运行maker3

```bash
mv genome.maker.output/ genome.maker.failed.output/
maker 2> maker_n1.error &
maker 2> maker_n2.error &
maker 2> maker_n3.error &
...
```

### 使用1.3.1版本的tRNAscan-SE，保证其与maker的兼容

```bash
wget http://lowelab.ucsc.edu/software/tRNAscan-SE.tar.gz
tar zxf tRNAscan-SE.tar.gz
cd tRNAscan-SE-1.3.1

vi Makerfile #将其中的$(HOME)变量修改为需要安装的路径
make && make install

echo 'PATH=$PATH:/your/path/to/tRNAscanSE/bin/' >> ~/.bashrc
echo 'PERL5LIB=$PERL5LIB:/your/path/to/tRNAscanSE/bin/' >> ~/.bashrc
source ~/.bashrc
```



## 参考来源

https://groups.google.com/forum/#!topic/maker-devel/at8neEnjbYk

https://blog.csdn.net/herokoking/article/details/77853662

<hr />