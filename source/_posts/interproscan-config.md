---
title: InterProScan的安装与使用
tags: [InterPro]
date: 2018-09-13 15:27:13
permalink:
categories:
description:
---
<p class="description">InterProScan是搜索InterPro数据库的推荐程序，常用于注释蛋白序列</p>

<!-- more -->

## 安装要求

- 64-bit Linux
- Perl 5 (default on most Linux distributions)
- Python 3 (InterProScan 5.30-69.0 onwards)
- Oracle's Java JDK/JRE version 8 (InterProScan 5.17-56.0 onwards)
- Environment variables set 
  - $JAVA_HOME should point to the location of the JVM
  - $JAVA_HOME/bin should be added to the $PATH

## 下载与安装

```bash
# 下载核心程序
mkdir my_interproscan
cd my_interproscan
wget ftp://ftp.ebi.ac.uk/pub/software/unix/iprscan/5/5.30-69.0/interproscan-5.30-69.0-64-bit.tar.gz
wget ftp://ftp.ebi.ac.uk/pub/software/unix/iprscan/5/5.30-69.0/interproscan-5.30-69.0-64-bit.tar.gz.md5

# 由于程序很大，推荐进行MD5校验，保证数据准确:
md5sum -c interproscan-5.30-69.0-64-bit.tar.gz.md5
# Must return *interproscan-5.30-69.0-64-bit.tar.gz: OK*
# If not - try downloading the file again as it may be a corrupted copy. 

# 解压程序，注意使用参数p保留文件的权限信息
tar -pxvzf interproscan-5.30-69.0-*-bit.tar.gz
```

```bash
# 下载Panther Models
cd [InterProScan5 home]/data/
wget ftp://ftp.ebi.ac.uk/pub/software/unix/iprscan/5/data/panther-data-12.0.tar.gz
wget ftp://ftp.ebi.ac.uk/pub/software/unix/iprscan/5/data/panther-data-12.0.tar.gz.md5
# 下载完成后进行MD5校验
md5sum -c panther-data-12.0.tar.gz.md5
# This must return *panther-data-12.0.tar.gz: OK*
# If not - try downloading the file again as it may be a corrupted copy.
cd [InterProScan5 home]/data
# 在data文件夹中解压数据库
tar -pxvzf panther-data-12.0.tar.gz
```

## 运行测试数据

```bash
./interproscan.sh
# 短暂等待后，即会输出帮助信息
# 使用程序自带的测试数据进行测试
./interproscan.sh -i test_proteins.fasta -f tsv
# 程序不联网，使用本地的Lookup Service
./interproscan.sh -i test_proteins.fasta -f tsv -dp
```

## 提升程序运行效率

### 多线程设置

在单机运行InterProScan时，可以在配置文件`interproscan.properties`中设置运行时的线程数，其默认设置如下

```
# Set the number of embedded workers to the number of processors that you would like to employ
# on the machine you are using to run InterProScan.
#number of embedded workers  a master process can have
number.of.embedded.workers=6
maxnumber.of.embedded.workers=8
```

对于一个40核的机器，[推荐](https://github.com/ebi-pf-team/interproscan/wiki/ImprovingPerformance#how-to-configure-cpu-usage---example-cases)的设置如下

```
#Number of embedded workers at start time
number.of.embedded.workers=1
#Maximum number of embedded workers
maxnumber.of.embedded.workers=35
```

### 将输入数据分组

推荐单次输入蛋白序列10000条，核酸序列1000条。若输入数据过多，建议将其分组运行。

### 精简输出结果

使用`-appl`参数设置比对的项目，减少程序耗时

## 常见问题

### RPSBlast 错误

```bash
bin/blast/ncbi-blast-2.6.0+/rpsblast: error while loading shared libraries: libgnutls.so.28: cannot open shared object file: No such file or directory
```

这是由于InterProScan自带的blast+中rpsblast的编译环境与本机环境不同。我们可以通过替换这支程序来解决。下载ncbi提供的已编译版本，使用其中的rpsblast替换InterProScan自带的rpsblast即可。

```bash
wget ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/2.6.0/ncbi-blast-2.6.0+-x64-linux.tar.gz
tar xvf ncbi-blast-2.6.0+-x64-linux.tar.gz

# backup original version
cp bin/blast/ncbi-blast-2.6.0+/rpsblast bin/blast/ncbi-blast-2.6.0+/rpsblast.bak

# copy the new version to the binary folder
cp ncbi-blast-2.6.0+/bin/rpsblast bin/blast/ncbi-blast-2.6.0+/rpsblast
```

### 序列输入错误

进行比对的蛋白序列中不应含有星号，否则将有如下报错。将序列中的星号删除即可。

> You have submitted a protein sequence which contains an asterix (\*). This may be from an ORF prediction program. '*' is not a valid IUPAC amino acid character and amino acid sequences which go through our pipeline should not contain it. Please strip out all asterix characters from your sequence and resubmit your search.

## 参考来源

https://github.com/ebi-pf-team/interproscan/wiki/HowToDownload

https://github.com/ebi-pf-team/interproscan/wiki/HowToRun

https://github.com/ebi-pf-team/interproscan/issues/44

https://github.com/ebi-pf-team/interproscan/wiki/FAQ

https://github.com/ebi-pf-team/interproscan/wiki/ImprovingPerformance

<hr />