---
title: conda源的配置
tags: [conda]
date: 2019-03-03 17:05:12
permalink:
categories:
description:
---
<p class="description">conda channel的设置</p>

<!-- more -->

## conda升级

conda提示的升级方案为

```bash
conda update -n base conda
```

按照提示进行升级，但升级提示竟然会将原本3.6版本的`python`降级至2.7

```bash
The following packages will be DOWNGRADED:

    python:          3.6.6-h5001a0f_3       https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge --> 2.7.15-h721da81_1008      https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
```

这表明，conda源配置中的`conda-forge`优先级过高，会出现一定的问题。检查一下原来的`.condarc`文件。

```bash
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
#  - bioconda
#  - conda-forge
#  - defaults
  - r
show_channel_urls: true
```

竟然将官方的defaults源注释掉了😓

不合理的顺序还可能会导致意想不到的[错误](http://vincebuffalo.org/notes/2017/08/28/notes-on-anaconda.html)，举例如下

>并且举了一个例子,如果你有一个X包来自于`conda-forge`,一个Y包来自于`biconda`才能让Z包顺利工作.但是在YAML里面不知道X,Y那个包是来自于`conda-forge`,那个是来自于`bioconda`. 比如说`conda-forge`有一个`bzip2-1.0.6`,里面有对应的动态`.so`文件,而`defaults`也有`bzip2-1.0.6`,但是没有`.so`文件. 但是根据按照顺序,conda先从`default`里面找到了`bzip2-1.0.6`, 结果你就会在运行软件的时候找不到依赖文件.

但hoptop[认为](https://www.jianshu.com/p/7025139ac7d4)一定要将default源放在最后，我认为还是不能同意。因为上述的conda更新问题就是由于default源（或第三方镜像的default源）的优先级低于`conda-forge`。因此，我认为至少`conda-forge`的优先级不宜过高。

## 合理配置conda源顺序

参照前人的[方案](https://shengxin.ren/article/432)，我们需要将`conda-forge`的优先级降低，仅作为官方源和`bioconda`源的补充。修改后的配置如下：

```bash
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - bioconda
  - defaults
  - r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - conda-forge
show_channel_urls: true
```

## 参考来源

https://shengxin.ren/article/432

https://www.jianshu.com/p/7025139ac7d4

http://vincebuffalo.org/notes/2017/08/28/notes-on-anaconda.html

<hr />