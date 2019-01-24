---
title: R-3.5.1安装
tags: [R, clusterProfiler]
date: 2018-11-24 16:35:20
permalink:
categories:
description:
---
<p class="description">conda安装R</p>

<!-- more -->

## conda安装

```bash
conda create -n R-3.5.1 R=3.5.1
```

但在安装完成后，运行R出现如下报错：

```bash
(R-3.5.1) [shurh@cluster EggNOG]$ R
/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/bin/exec/R: error while loading shared libraries: libbz2.so.1.0: cannot open shared object file: No such file or directory
```

此时，覆盖安装bzip2可解决问题

```bash
conda install -c conda-forge -c bioconda bzip2
```

## 安装软件包

### 安装clusterProfiler

在R 3.5版本后，bioconductor采用了新的安装方法，直接调用BioManager中的intall进行安装

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("clusterProfiler", version = "3.8")
```

但在编译结果提示，部分包没有编译完成。

```R
* installing *source* package ‘units’ ...
** package ‘units’ successfully unpacked and MD5 sums checked
configure: units: 0.6-1
checking whether the C++ compiler works... yes
checking for C++ compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
checking how to run the C++ preprocessor... g++ -E
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking for stdbool.h that conforms to C99... no
checking for _Bool... no
checking for error_at_line... yes
checking for gcc... gcc -std=gnu99
checking whether we are using the GNU C compiler... yes
checking whether gcc -std=gnu99 accepts -g... yes
checking for gcc -std=gnu99 option to accept ISO C89... none needed
checking for XML_ParserCreate in -lexpat... no
checking udunits2.h usability... no
checking udunits2.h presence... no
checking for udunits2.h... no
checking udunits2/udunits2.h usability... no
checking udunits2/udunits2.h presence... no
checking for udunits2/udunits2.h... no
checking for ut_read_xml in -ludunits2... no
configure: error: in `/tmp/RtmpmHkkL2/R.INSTALLf29a648ed03a/units':
configure: error:
--------------------------------------------------------------------------------
  Configuration failed because libudunits2.so was not found. Try installing:
    * deb: libudunits2-dev (Debian, Ubuntu, ...)
    * rpm: udunits2-devel (Fedora, EPEL, ...)
    * brew: udunits (OSX)
  If udunits2 is already installed in a non-standard location, use:
    --configure-args='--with-udunits2-lib=/usr/local/lib'
  if the library was not found, and/or:
    --configure-args='--with-udunits2-include=/usr/include/udunits2'
  if the header was not found, replacing paths with appropriate values.
  You can alternatively set UDUNITS2_INCLUDE and UDUNITS2_LIBS manually.
--------------------------------------------------------------------------------

See `config.log' for more details
ERROR: configuration failed for package ‘units’
* removing ‘/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/units’
ERROR: dependency ‘units’ is not available for package ‘ggforce’
* removing ‘/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/ggforce’
ERROR: dependency ‘ggforce’ is not available for package ‘ggraph’
* removing ‘/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/ggraph’

The downloaded source packages are in
        ‘/tmp/RtmpYqu9Z7/downloaded_packages’
Updating HTML index of packages in '.Library'
Making 'packages.html' ... done
Warning messages:
1: In install.packages("ggraph") :
  installation of package ‘units’ had non-zero exit status
2: In install.packages("ggraph") :
  installation of package ‘ggforce’ had non-zero exit status
3: In install.packages("ggraph") :
  installation of package ‘ggraph’ had non-zero exit status
```

此时选择先安装ggraph

```R
BiocManager::install("graph", version = "3.8")
```

出现提示，需要安装`udunits2`，此时同样选择conda安装

```bash
conda install -c conda-forge udunits2 
```

完成后，即可重新安装clusterProfiler

## 参考来源

https://stackoverflow.com/questions/27893230/installation-of-package-file-path-had-non-zero-exit-status-in-r

<hr />