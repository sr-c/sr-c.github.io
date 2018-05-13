---
title: MECAT安装
date: 2018-05-12 22:57:53
tags: debug
---
MECAT依赖`HDF5`和`dextract`，README中推荐的安装方法，并不十分友好，记录如下。
<!-- more -->
## 安装HDF5
HDF是比较成熟的一种库文件格式，目前流行的版本是HDF5，MECAT的README中为从源码编译安装，多次尝试，编译均告失败。好在发现HDF5官网提供CentOS6的已编译版本，直接解压即可~~食~~使用😀。
## 安装dextract
从PacificBiosciences的库中git下载源码，dextract官方并未给出安装说明，按照MECAT提供的`dextract_makefile`进行编译，出现了如下`undefined reference`错误。
```bash
gcc -O3 -Wall -Wextra -Wno-unused-result -fno-strict-aliasing -I/Storage/data002/shurh/software/hdf5/include -L/Storage/data002/shurh/software/hdf5/lib -o dextract dextract.c DB.c QV.c -lhdf5 -lsz
/tmp/ccXFKK1s.o: In function `main':
dextract.c:(.text.startup+0x135): undefined reference to `parse_filter'
dextract.c:(.text.startup+0x27c): undefined reference to `initBaxData'
dextract.c:(.text.startup+0x33a): undefined reference to `getBaxData'
dextract.c:(.text.startup+0x35a): undefined reference to `nextSubread'
dextract.c:(.text.startup+0x36b): undefined reference to `nextSubread'
dextract.c:(.text.startup+0x38c): undefined reference to `evaluate_bax_filter'
dextract.c:(.text.startup+0x538): undefined reference to `sam_open'
dextract.c:(.text.startup+0x54e): undefined reference to `sam_header_process'
dextract.c:(.text.startup+0x58e): undefined reference to `sam_record_extract'
dextract.c:(.text.startup+0x5a1): undefined reference to `SAM_EOF'
dextract.c:(.text.startup+0x5b4): undefined reference to `evaluate_bam_filter'
dextract.c:(.text.startup+0xc35): undefined reference to `sam_close'
dextract.c:(.text.startup+0xfb0): undefined reference to `getBaxData'
dextract.c:(.text.startup+0x1096): undefined reference to `sam_open'
dextract.c:(.text.startup+0x129f): undefined reference to `parse_filter'
dextract.c:(.text.startup+0x1430): undefined reference to `printBaxError'
collect2: error: ld returned 1 exit status
make: *** [dextract] Error 1
```
按照其他同志的踩坑，发觉`dextract_makefile`并不完善， 缺少几个源文件和库文件链接，修改第7行:
    ${CC} $(CFLAGS) -I$(HDF5_INCLUDE) -L$(HDF5_LIB) -o dextract dextract.c sam.c bax.c expr.c DB.c QV.c -lhdf5 -lz -lsz

参考来源：
https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8/hdf5-1.8.15-patch1/bin/linux-centos6-x86_64/hdf5-1.8.15-patch1-linux-centos6-x86_64-shared.tar.gz
https://github.com/xiaochuanle/MECAT/issues/19