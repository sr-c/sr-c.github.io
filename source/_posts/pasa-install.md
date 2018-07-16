---
title: PASA的安装
tags: [pasa,perl]
date: 2018-07-15 11:59:02
permalink:
categories:
description:
---
<p class="description">PASA在`2.3.0`版本后加入了`SQLite`的支持，解放了其对`Mysql`的依赖，使其对普通用户更加友好，但是其安装还是有许多依赖问题需要解决。</p>

<!-- more -->

## 安装PASA

```bash
cd ~/software/
wget https://github.com/PASApipeline/PASApipeline/releases/download/pasa-v2.3.3/PASApipeline-v2.3.3.tar.gz
tar zxf PASApipeline-v2.3.3.tar.gz -C ~/opt/
mv ~/opt/PASApipeline-pasa-v2.3.3 ~/opt/pasa-v2.3.3
cd ~/opt/pasa-v2.3.3
make
echo '$PASAHOME=~/opt/pasa-v2.3.3/' >> ~/.bashrc
source ~/.bashrc
```

## 环境测试

```bash
cd $PATHHOME/sample_data
./runMe.SQLite.sh
```

测试运行约1h后得到输出结果

> sample_mydb_pasa.assemblies.fasta
>
> sample_mydb_pasa.pasa_assemblies.gff3
>
> sample_mydb_pasa.pasa_assemblies_described.txt

## 安装perl模块

安装PASA最大的困难就在于`perl`模块的配置。本文参考使用`perlbrew`与`cpanm`配置perl模块

```bash
cpanm DBD::SQLite
```

### 报错解决

安装过程中由于之前配置的`conda`环境的干扰，使得在运行`sample data`时出现报错，需要缺少perl模块支持，使用`cpanm`安装模块，却出现编译错误，提示缺少  `db.h`，但`/usr/include/db.h`存在，一时未能排除BUG

整理思路后发觉应当在`~/.bashrc`中屏蔽`conda`环境后重新配置。

但仍然出现`/usr/bin/perl: xxx symbol lookup error`错误，报错原因应当是原`conda`环境中的perl安装的`DBD::SQLite`模块与现有环境不兼容。因此，需要先卸载已安装的模块，再重新安装。

> p.s. [其他解决方案](https://www.jianshu.com/p/9e90b3524fe2)，未验证

### perl模块的卸载

`cpan`工具只管理模块的安装，却[不管卸载](http://stackoverflow.com/questions/7777252/uninstall-all-perl-modules-installed-by-cpan )。使用`App::pmuninstall`模块进行模块的删除

```bash
cpanm App::pmuninstall
```

使用很方便

```bash
pm-uninstall DBD::SQLite
```

此外，还可使用 `App::pmodinfo`模块查看perl模块的相关信息。（安装依赖38个模块，较耗时）

> -v --version
>
> -f --full
>
> -h --hash
>
> -l --local-modules
>
> -u --check-updates

## 安装其他依赖软件

### 安装GMAP

### 安装BLAT

### 安装FASTA

```bash
cd ~/software
wget http://faculty.virginia.edu/wrpearson/fasta/fasta36/fasta-36.3.8g.tar.gz
tar zxf fasta-36.3.8g.tar.gz -C ~/opt/
cd ~/opt/fasta-36.3.8g/src/
make -f ../make/Makefile.linux_sse2 all
cd ../bin/
ln -s fasta36 fasta
echo 'PATH=$PATH:~/opt/fasta-36.3.8g/bin/' >> ~/.bashrc
source ~/.bashrc
```

> 按照《NGS生物信息分析v6.0》，不应当安装fasta36或以上版本，否则运行PASA进行GFF3文件更新时，会调用fasta命令，导致内存耗尽，同时得不到结果。（pasa-v2.2.0）
>
> 不知目前版本（v2.3.3）不知是否修复了此BUG，留此存照。

## 安装UniVec数据库

`seqclean`在v2.3.3版本中默认位于`$PASAHOME/bin/`路径中，不需另行安装。因此只需安装其依赖的数据库即可，在此以`UniVec`数据库为例（大多数情况下，`UniVec`即可满足需求）。

```bash
mkdir ~/database/UniVec
cd ~/database/UniVec
wget ftp://ftp.ncbi.nlm.nih.gov/pub/UniVec/UniVec
formatdb -t UniVec -i UniVec -p F -o T
$PASAHOME/bin/seqclean transcripts.fasta -v ~/database/UniVec/UniVec
```

## 参考来源

https://github.com/PASApipeline/PASApipeline/wiki/Pasa_installation_instructions

https://blog.csdn.net/u011450367/article/details/41522729

http://stackoverflow.com/questions/7777252/uninstall-all-perl-modules-installed-by-cpan 

<hr />