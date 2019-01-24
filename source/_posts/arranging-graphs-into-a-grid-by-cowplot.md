---
title: 使用cowplot完成图片排版
tags: [R, cowplot, ggimage]
date: 2018-12-06 15:18:16
permalink:
categories:
description:
---
<p class="description">读入外部图片，并使用cowpolt组图</p>

<!-- more -->

## 默认方案

使用`cowplot`提供的`draw_image`函数读入图片

```R
library(cowplot)
p1 <- ggdraw() + draw_image("myFirstAlignment.pdf")
```

## 存在问题

读入的图片存在太多的空白，需要手动切割，或者使用`paperWidth`和`paperHeight`来调整，此外，`draw_image`不指出指定分辨率，读图出来的分辨率太低。

## 解决方案

使用`ggimage::image_read2`函数导入外部图片，保证图片分辨率并使用`ggplotify::as.ggplot`转化为`ggplot`对象，再使用`cowplot`组图。

```R
#安装ggimage最新版
setRepositories(ind=1:2)
devtools::install_github("GuangchuangYu/ggimage")
#安装ggplotify
devtools::install_github("GuangchuangYu/ggplotify")
```

### 安装依赖

```R
Package Magick++ was not found in the pkg-config search path.
Perhaps you should add the directory containing `Magick++.pc'
to the PKG_CONFIG_PATH environment variable
No package 'Magick++' found
Using PKG_CFLAGS=
Using PKG_LIBS=-lMagick++-6.Q16
------------------------- ANTICONF ERROR ---------------------------
Configuration failed because Magick++ was not found. Try installing:
 - deb: 'libmagick++-dev' (Debian, Ubuntu)
 - rpm: 'ImageMagick-c++-devel' (Fedora, CentOS, RHEL)
 - csw: 'imagemagick_dev' (Solaris)
On MacOS it is recommended to use install ImageMagick-6 from homebrew
with extra support for fontconfig and rsvg rendering:
   brew reinstall imagemagick@6 --with-fontconfig --with-librsvg
For older Ubuntu versions Trusty (14.04) and Xenial (16.04) use our PPA:
   sudo add-apt-repository -y ppa:opencpu/imagemagick
   sudo apt-get update
   sudo apt-get install -y libmagick++-dev
If Magick++ is already installed, check that 'pkg-config' is in your
PATH and PKG_CONFIG_PATH contains a Magick++.pc file. If pkg-config
is unavailable you can set INCLUDE_DIR and LIB_DIR manually via:
R CMD INSTALL --configure-vars='INCLUDE_DIR=... LIB_DIR=...'
--------------------------------------------------------------------
ERROR: configuration failed for package ‘magick’
* removing ‘/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/magick’
Error in i.p(...) :
  (converted from warning) installation of package ‘magick’ had non-zero exit status
```

根据报错信息，环境中缺少安装需求的`Magick++.pc`，此时可通过yum查找哪些软件包可以提供

```bash
#yum查找提供Magick++.pc的软件包
yum provides '*/Magick++.pc'

#结果发现所需的两个包
ImageMagick-c++-devel-6.7.2.7-6.el6.x86_64 : C++ bindings for the ImageMagick library
Repo        : base
Matched from:
Filename    : /usr/lib64/pkgconfig/Magick++.pc

ImageMagick-c++-devel-6.7.2.7-6.el6.i686 : C++ bindings for the ImageMagick library
Repo        : base
Matched from:
Filename    : /usr/lib/pkgconfig/Magick++.pc

```

安装完成后仍然报错

```R
Error: package or namespace load failed for ‘magick’ in dyn.load(file, DLLpath = DLLpath, ...):
 unable to load shared object '/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/magick/libs/magick.so':
  /Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/magick/libs/magick.so: undefined symbol: _ZN6Magick5Image5writeEPNS_4BlobERKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEm
Error: loading failed
Execution halted
ERROR: loading failed
* removing ‘/Storage/data002/shurh/miniconda3/envs/R-3.5.1/lib/R/library/magick’
Error in i.p(...) :
  (converted from warning) installation of package ‘magick’ had non-zero exit status
```

一阵搜索后无果，但明显是`magick`仍然未能安装完成。最终，通过`conda`安装`r-magick`从而完成`magick`的安装。

```bash
conda install -c conda-forge r-magick
```

此时安装的`r-magick`为1.9版本，而`ggimage`要求从源码安装的`magick`则为2.0版本，若按要求升级则同样会报错，此时可选择不升级，保持1.9版本仍然可完成`ggimage`的安装

## 实例演示

```R
plot_grid(
  plot_grid(p1, p2$gtable, ncol=2, rel_widths=c(.6, 1), labels=LETTERS[1:2]),
  p3, ncol=1, labels=c("", "C"))
```



## 参考来源

https://cran.r-project.org/web/packages/cowplot/vignettes/introduction.html

https://cran.r-project.org/web/packages/ggimage/vignettes/ggimage.html

https://guangchuangyu.github.io/cn/2017/04/ggimage/

https://stackoverflow.com/questions/20416857/how-do-i-install-imagemagick-devel-libraries-on-fedora

<hr />