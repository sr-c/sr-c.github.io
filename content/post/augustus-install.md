---
title: AUGUSTUS的普通用户安装
tags: [AUGUSTUS,bamtools,debug]
date: 2018-09-04 18:07:53
permalink:
categories:
description:
---
<p class="description">AUGUSTUS是应用非常普遍的基因预测软件，但是其安装依赖bamtools，而且需要很多root权限操作。因此，以普通用户权限安装augustus-3.3.1的经历记录如下。</p>

<!-- more -->

## 直接编译AUGUSTUS

```bash
make[2]: Entering directory `$HOME/software/augustus-3.3.1/auxprogs/bam2hints'
make[2]: warning: jobserver unavailable: using -j1.  Add `+' to parent make rule.
g++ -Wall -O2    -c bam2hints.cc -o bam2hints.o -I/usr/include/bamtools
bam2hints.cc:16:10: fatal error: api/BamReader.h: No such file or directory
 #include <api/BamReader.h>
          ^~~~~~~~~~~~~~~~~
compilation terminated.
make[2]: *** [bam2hints.o] Error 1
make[2]: Leaving directory `$HOME/software/augustus-3.3.1/auxprogs/bam2hints'
make[1]: *** [all] Error 2
make[1]: Leaving directory `$HOME/software/augustus-3.3.1/auxprogs'
make: *** [all] Error 2
```

直接编译`AUGUSTUS`会在编译`bam2hints`时出错。因此，我们需要安装`bamtools`。

## 安装bamtools

### 一键解决方案

```bash
sudo apt-get install bamtools libbamtools-dev
```

但是，我们没有root权限，只能手动编译。

### 普通用户安装bamtools需要指定安装目录

```bash
wget https://github.com/pezmaster31/bamtools/archive/v2.4.1.tar.gz -O bamtools-2.4.1.tar.gz
tar zxf bamtools-2.4.1.tar.gz
cd bamtools-2.4.1
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/your/path/to/bamtools ..
make
make install
```

我目前的服务器环境比较复杂。CentOS 6.9，其默认的GCC版本是4.4.7，在编译很多软件时版本过低。目前将其升级到了7.3.0，但未保留原本的4.4.7环境。因此，在编译bamtools过程中有些[挑剔软件版本](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=811856)。

对于≤2.4.0版本的bamtools，编译时都会出现如下报错。≥2.4.2版本的bamtools则要求cmake版本高于3.0，而本机的cmake版本是2.8.12.2。因此，在尽量少折腾的原则下，我们选择2.4.1版本。

<details>
    <summary>▼筛选可用的bamtools版本</summary>
    <h4> 编译bamtools-2.3.0 或2.4.0</h4>
    <p> 出现如下错误，尚未发现解决方案 </p>
    $HOME/software/bamtools-2.3.0/src/toolkit/bamtools_resolve.cpp:415:74: error: no matching function for call to ‘make_pair<std::cxx11::string, bool>(gnu_cxx::alloc_traitsstd::allocator<std::cxx11::basic_string<char > >::value_type&, bool)’
         resolver.ReadNames.insert( make_pair<string,bool>(fields[1], true) ) ;
                                                                          ^
In file included from /usr/local/include/c++/7.3.0/bits/stl_algobase.h:64:0,
                 from /usr/local/include/c++/7.3.0/bits/char_traits.h:39,
                 from /usr/local/include/c++/7.3.0/string:40,
                 from $HOME/software/bamtools-2.3.0/src/toolkit/bamtools_tool.h:14,
                 from $HOME/software/bamtools-2.3.0/src/toolkit/bamtools_resolve.h:13,
                 from $HOME/software/bamtools-2.3.0/src/toolkit/bamtools_resolve.cpp:10:
    <h4> 编译bamtools-2.4.2</h4>
    <p> 提示需求更高版本的CMake</p>
    CMake Error at CMakeLists.txt:9 (cmake_minimum_required):
      CMake 3.0 or higher is required.  You are running version 2.8.12.2
        -- Configuring incomplete, errors occurred!
</details>

### 编译bamtools-2.4.1

安装完成后，运行bamtools程序，提示缺少`libbamtools.so.2.4.1`

```bash
$ ~/opt/bamtools-2.4.1/bin/bamtools
$HOME/opt/bamtools-2.4.1/bin/bamtools: error while                                                 loading shared libraries: libbamtools.so.2.4.1: cannot open shared                                                 object file: No such file or directory
```

`find ~/opt -name libbamtools.so.2.4.1`搜寻该文件。然后将`libbamtools.so.2.4.1`软链接到~/opt/bamtools-2.4.1/lib/目录下，即可正常启动`bamtools`

### 修改bam2hints 与filterBam的Makefile，使其与手动编译的bamtools配合

```bash
First, go to the “./auxprogs/bam2hints” directory and make the following changes for the Makefile:

Add:
BAMTOOLS = $HOME/opt/bamtools-2.4.1

Replace:
INCLUDES = /usr/include/bamtools
By:
INCLUDES = $(BAMTOOLS)/include

Replace:
LIBS = -lbamtools -lz
By:
LIBS = $(BAMTOOLS)/lib/bamtools/libbamtools.a -lz

Then, go to the “augustus-3.2.3/auxprogs/filterBam/src” directory and make the following changes for the Makefile:
Replace:

BAMTOOLS = /usr/include/bamtools
By:
BAMTOOLS = $HOME/opt/bamtools-2.4.1

Replace:
INCLUDES = -I$(BAMTOOLS) -Iheaders -I./bamtools
By:
INCLUDES = -I$(BAMTOOLS)/include -Iheaders -I./bamtools

Replace:
LIBS = -lbamtools -lz
By:
LIBS = $(BAMTOOLS)/lib64/libbamtools.a -lz
```

## 编译bam2wig

编译好bamtools后，应当向系统目录拷贝一些库文件，但限于我们没有root权限，只能修改Makefile，手动指向这些库文件。

### 编译bam2wig依赖bcftools, htslib, samtools, tabix四款软件

从其github站点，或[官网](http://www.htslib.org/download/)下载，解压后直接编译。其中，tabix已经并入了htslib

```sh
./configure --prefix=/where/to/install
make
make install
```

### 修改bam2wig的Makefile，使其与上述软件配合

```bash
# SAMTOOLS=$(TOOLDIR)/samtools/
SAMTOOLS=$HOME/software/samtools-1.9

#HTSLIB=$(TOOLDIR)/htslib/
HTSLIB=$HOME/software/htslib-1.9

# BCFTOOLS=$(TOOLDIR)/bcftools/
BCFTOOLS=$HOME/software/bcftools-1.9

# TABIX=$(TOOLDIR)/tabix/
TABIX=$HOME/software/htslib-1.9
```

> 注意，此处的路径`$HOME/software/`其实是解压后的软件包编译的路径，并不是软件最终安装的路径。否则，会出现如下报错。

```bash
make[2]: Entering directory `$HOME/software/augustus-3.3.1/auxprogs/bam2wig'
make[2]: warning: jobserver unavailable: using -j1.  Add `+' to parent make rule.
gcc -Wall -O2 -I$HOME/opt/samtools-1.8/ -I. -I$HOME/opt/htslib-1.9/ -I$HOME/opt/bcftools-1.9/ -I$HOME/opt/htslib-1.9/ -c bam2wig.c -o bam2wig.o
bam2wig.c:18:10: fatal error: sam.h: No such file or directory
 #include "sam.h"
          ^~~~~~~
compilation terminated.
```

### 编译bam2wig过程中报错

```bash
hfile_s3.c:(.text+0x8d7): undefined reference to `EVP_sha1'
hfile_s3.c:(.text+0x8fd): undefined reference to `HMAC'
collect2: error: ld returned 1 exit status
```

[解决方案](https://stackoverflow.com/questions/36033980/undefined-reference-evp-sha1-hmac-id-returned-1-exit-status)

```bash
hfile_libcurl.c:(.text+0x12aa): undefined reference to `curl_global_cleanup'
collect2: error: ld returned 1 exit status
```

[解决方案](https://stackoverflow.com/questions/11884525/cant-compile-in-linux-with-curl-undefined-reference)

因此，我们还需修改bam2wig路径中的Makefile，向其中的`LIBS`项末尾添加`-lcrypto` `-lcurl`两个字段补充缺少的库文件。

## 再次编译AUGUSTUS

```bash
##清除之前未成功编译的中间文件，回复初始状态
make clean
##多线程编译缩短时间
make -j 8
echo 'PATH=$PATH:~/opt/augustus/bin:~/opt/augustus/scripts' >> ~/.bashrc
##可选步骤，手动设置配置文件夹的路径。如未设置，默认会搜寻可执行文件相对路径../config中的文件。
echo 'AUGUSTUS_CONFIG_PATH=/my_path_to_AUGUSTUS/augustus/config/' >> ~/.bashrc
```

## 总结

经过上述步骤，在普通用户权限下解决好`bam2hints`与`bam2wig`的依赖，并修改其Makefile其实适应手动安装的配置环境，从而完成普通用户的AUGUSTUS安装。

## 参考来源

https://github.com/Gaius-Augustus/Augustus

https://genehub.wordpress.com/2018/05/16/augustus-3-3%E7%9A%84%E5%AE%89%E8%A3%85/

https://iamphioxus.org/2017/05/08/installing-augustus-with-manual-bamtools-installation/

<hr />