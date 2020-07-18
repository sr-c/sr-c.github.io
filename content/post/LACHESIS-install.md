---
title: LACHESIS安装
tags: [LACHESIS,Hi-C]
date: 2019-03-19 21:12:12
permalink:
categories:
description:
---
<p class="description">LACHESIS的编译安装</p>

<!-- more -->

## 系统依赖

`LACHESIS`的系统依赖有

- gcc, the C++ compiler (<http://gcc.gnu.org/>)
- The zlib compression library (<http://www.zlib.net/>)
- The boost C++ libraries (<http://www.boost.org/>)
- The SAMtools toolkit for handling SAM/BAM files (<http://samtools.sourceforge.net/>) (make sure to use version 0.1.19 or older)

其中，`boost`的版本应该高于`1.52.0`，低于`1.67.0`，`samtools`的版本不高于`0.1.19`

此外，LACHESIS需要系统的堆栈大小最小为10 MB. 可使用`ulimit -s`查看，若小于10240，可使用`ulimit -s 10240`设定。

### 编译安装boost

```bash
tar zxf boost_1_53_0.tar.gz
./bootstrap.sh --with-libraries=all --with-toolset=gcc
./b2 toolset=gcc #此步骤过程较长
./b2 install --prefix=/path/to/istall/boost_1_53_0
./bjam install --prefix=/path/to/istall/boost_1_53_0
```

此过程中会得到许多failed，error如下

```
...failed gcc.compile.c++ bin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi/cpplexer/re2clex/cpp_re.o...
...skipped <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>libboost_wave.a(clean) for lack of <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>cpplexer/re2clex/cpp_re.o...
...skipped <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>libboost_wave.a for lack of <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>cpplexer/re2clex/cpp_re.o...
...skipped <pstage/lib>libboost_wave.a for lack of <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>libboost_wave.a...
...failed updating 88 targets...
...skipped 78 targets...
...updated 802 targets...

...failed gcc.compile.c++ bin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi/cpplexer/re2clex/cpp_re.o...
...skipped <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>libboost_wave.a(clean) for lack of <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>cpplexer/re2clex/cpp_re.o...
...skipped <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>libboost_wave.a for lack of <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>cpplexer/re2clex/cpp_re.o...
...skipped <p/Storage/data002/shurh/opt/boost_1_53_0/lib>libboost_wave.a for lack of <pbin.v2/libs/wave/build/gcc-7.3.0/release/link-static/threading-multi>libboost_wave.a...
...failed updating 88 targets...
...skipped 78 targets...
...updated 10108 targets...

```

### 编译安装samtools

```bash
./Makefile
```

### 编译LACHESIS

```bash
export LACHESIS_BOOST_DIR=/path/to/boost_1_53_0
export LACHESIS_SAMTOOLS_DIR=/path/to/samtools-0.1.19
./configure --with-samtools=/path/to/samtools-0.1.19 --with-boost=/path/to/boost_1_53_0/
```

报错

```bash
checking for Boost headers version >= 1.52.0... /Storage/data002/shurh/opt/boost_1_53_0//include
checking for Boost's header version... 1_53
checking for the toolset name used by Boost for g++... configure: WARNING: could not figure out which toolset name to use for g++

checking boost/system/error_code.hpp usability... yes
checking boost/system/error_code.hpp presence... yes
checking for boost/system/error_code.hpp... yes
checking for the Boost system library... no
configure: error: cannot find the flags to link with Boost system
```

提示boost缺少system库文件，查看

```bash
$ ls ~/opt/boost_1_53_0/lib/
libboost_chrono.a             libboost_math_c99f.so.1.53.0  libboost_prg_exec_monitor.a
libboost_context.a            libboost_math_c99l.a          libboost_prg_exec_monitor.so
libboost_context.so           libboost_math_c99l.so         libboost_prg_exec_monitor.so.1.53.0
libboost_context.so.1.53.0    libboost_math_c99l.so.1.53.0  libboost_python.a
libboost_date_time.a          libboost_math_c99.so          libboost_python.so
libboost_date_time.so         libboost_math_c99.so.1.53.0   libboost_python.so.1.53.0
libboost_date_time.so.1.53.0  libboost_math_tr1.a           libboost_random.a
libboost_exception.a          libboost_math_tr1f.a          libboost_random.so
libboost_graph.a              libboost_math_tr1f.so         libboost_random.so.1.53.0
libboost_iostreams.a          libboost_math_tr1f.so.1.53.0  libboost_signals.a
libboost_iostreams.so         libboost_math_tr1l.a          libboost_signals.so
libboost_iostreams.so.1.53.0  libboost_math_tr1l.so         libboost_signals.so.1.53.0
libboost_math_c99.a           libboost_math_tr1l.so.1.53.0  libboost_timer.a
libboost_math_c99f.a          libboost_math_tr1.so
libboost_math_c99f.so         libboost_math_tr1.so.1.53.0
```

确实缺少`libboost_system.so`, 而这在`./bjam install`过程中的报错信息中存在

```bash
...failed gcc.compile.c++ bin.v2/libs/system/build/gcc-7.3.0/release/link-static/threading-multi/error_code.o...
...skipped <pbin.v2/libs/system/build/gcc-7.3.0/release/link-static/threading-multi>libboost_system.a(clean) for lack of <pbin.v2/libs/system/build/gcc-7.3.0/release/link-static/threading-multi>error_code.o...
...skipped <pbin.v2/libs/system/build/gcc-7.3.0/release/link-static/threading-multi>libboost_system.a for lack of <pbin.v2/libs/system/build/gcc-7.3.0/release/link-static/threading-multi>error_code.o...
...skipped <p/usr/local/lib>libboost_system.a for lack of <pbin.v2/libs/system/build/gcc-7.3.0/release/link-static/threading-multi>libboost_system.a...
```

说明在编译过程中，`libboost_system`由于某些错误被滤过了，没有被编译。

## 自行使用conda配置boost环境

```bash
conda create -n LACHESIS python=2.7
source activate LACHESIS
conda install boost=1.57 -y
export LACHESIS_BOOST_DIR=/path/miniconda/envs/LACHESIS
export LACHESIS_SAMTOOLS_DIR=/path/to/samtools-0.1.19
./configure --with-samtools=/path/to/samtools-0.1.19 --with-boost=/path/to/boost_1_53_0/
```

Boost system library通过，但得到后续报错

```bash
checking for boost/program_options.hpp... yes
checking for the Boost program_options library... no
configure: error: cannot find the flags to link with Boost program_options
```

但是`/path/miniconda/envs/LACHESIS/lib`中是存在`libboost_program_options.so`的

查看`./configure`中的`deflate`

```
for ac_header in zlib.h
do :
  ac_fn_cxx_check_header_mongrel "$LINENO" "zlib.h" "ac_cv_header_zlib_h" "$ac_include
if test "x$ac_cv_header_zlib_h" = xyes; then :
  cat >>confdefs.h <<_ACEOF
#define HAVE_ZLIB_H 1
_ACEOF

else
  as_fn_error $? "libz is required." "$LINENO" 5
fi

done

{ $as_echo "$as_me:${as_lineno-$LINENO}: checking for library containing deflate" >&5
$as_echo_n "checking for library containing deflate... " >&6; }
if ${ac_cv_search_deflate+:} false; then :
  $as_echo_n "(cached) " >&6
else
  ac_func_search_save_LIBS=$LIBS
cat confdefs.h - <<_ACEOF >conftest.$ac_ext
/* end confdefs.h.  */
```

### zlib错误

https://daler.github.io/bioconda-docs/troubleshooting.html#zlib-errors

有时即使已经安装了zlib, 但在编译程序时仍然报错缺少`zlib.h`, 这常常时因为zlib的位置常常在编译文件中定义了，

```bash
unset LIBRARY_PATH CPATH C_INCLUDE_PATH PKG_CONFIG_PATH CPLUS_INCLUDE_PATH INCLUDE
```



手动修改`src/include/gtools`文件夹中`SAMStepper.h`和`SAMStepper.cc`的samtools头文件

## 参考来源

https://github.com/shendurelab/LACHESIS/issues/30#issuecomment-299240018

https://github.com/shendurelab/LACHESIS/issues/23

https://blog.csdn.net/yzf279533105/article/details/77658367

https://daler.github.io/bioconda-docs/troubleshooting.html#zlib-errors

https://www.tecmint.com/set-unset-environment-variables-in-linux/

<hr />