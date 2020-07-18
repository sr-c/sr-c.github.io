---
title: nf-core一步完成RNA-seq上游分析
tags: [nf-core,RNA-seq]
date: 2020-03-01 22:42:21
permalink:
categories:
description:
---
<p class="description">使用nf-core，一键完成RNA-seq的上游分析流程</p>

<!-- more -->

`nf-core`使用`nextflow`作为流程管理工具，搭建了一系列[分析流程](https://nf-co.re/pipelines)，现已发表在[Nat Biotechnol](https://dx.doi.org/10.1038/s41587-020-0439-x)。其中，最为常用，关注也最多的自然是RNA-seq的流程。简单的解决方案可拉到最后。

#安装

每个`nf-core`的流程都支持3种配置方式，`docker`, `singularity`与`conda`. 使用这3种方式都可以配置，但是，前两者的安装都需要root权限，而作为普通用户，你的选择可能还是只有可爱的`conda`了。

## Nextflow

首先需要环境中`java`的版本在1.8以上，然后安装到用户自定义的路径中，以方便后续的升级管理。

```bash
# Make sure that Java v8+ is installed:
java -version

# Install Nextflow
curl -fsSL get.nextflow.io | bash

# Add Nextflow binary to your user's PATH:
mv nextflow ~/bin/
# OR system-wide installation:
# sudo mv nextflow /usr/local/bin
```

或者，通过Bioconda安装

```bash
conda install -c bioconda nextflow
```

Nextflow的升级就很简单， `nextflow self-update` 或`conda update nextflow`（取决于你的安装方式）即可。

## 流程工具

Docker，Singularly或者Conda，前两者以容器形式运行，可重复性好，但其安装需要管理员权限。

首先，需要确认你已经安装好了所需要的软件环境(Nextflow + Docker / Singularity / Conda)，尝试Nextflow的"hello world"

```bash
  nextflow run hello
```

流程工具的运行需要联网，会自动运行最新的流程配置。如果需要离线运行指定流程，可以参考官方提供的说明。

```bash
nextflow run nf-core/rnaseq -profile test,<docker/singularity/conda>
```

测试运行`nf-core/rnaseq`流程。

注意，请配置好`conda`的环境，即`~/.condarc`，否则conda的连接速度可能会很慢，导致报错。推荐的源配置如下：

```json
channels:
  - bioconda
  - defaults
  - conda-forge
show_channel_urls: True
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
custom_channels:
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

#参考基因组

许多流程的序列比对，注释等流程都会用到参考基因组文件。`nf-core`默认可以使用[Illumina iGenomes](https://support.illumina.com/sequencing/sequencing_software/igenome.html)中支持物种的参考基因组。但是，如果需要使用自定义的参考基因组，也可以使用`--fasta`与`--gtf`参数，传递参考基因组序列与注释文件。

其中，注释文件的推荐格式为`gtf`，要求其中有`gene_biotype`属性，指定基因为`protein_coding`, `lincRNA`, `rRNA`或其他。

也可提供一个`gff`文件，但是其会被先转换为`gtf`文件。

gtf文件的格式最好以ENSEMBL版本为参考。若为GENCODE格式，则需提供`--gencode`参数注明。

# 其他

`Nextflow`的活动流程要求其进程需要一直运行，直至流程完成。因此，推荐将其运行在`screen`/`tmux`的后台环境中，或者投递到集群的作业系统中。

同时，官方还推荐在系统环境配置中限制`Nextflow`Java虚拟机的内存占用，向`~/.bashrc`或`~/.bash_profile`中添加

```bash
NXF_OPTS='-Xms1g -Xmx4g'
```

## 快速开始

一个命令完成RNA-seq的方式如下:

```bash
nextflow run nf-core/rnaseq \
--reads 'input_reads/*_{1,2}.clean.fq.gz' \ # 路径必须以单引号包括
--fasta /path/to/reference/genome/ref.fasta \
--gtf /path/to/reference/annotation/ref.gtf \
-profile conda \
--pseudo_aligner salmon \ # 除了HISAT2外，还使用salmon进行比对
-name name_for_the_pipeline_run \
--max_cpus 16 \
--max_memory '8.GB'
# --reverseStranded 
```

如果RNA-seq的是链特异性文库，可以手动指定文库的方向，如`--reverseStranded`指定在HISAT2比对时指定`--rna-strandness RF`

## 参考来源

https://nf-co.re/pipelines

https://github.com/nf-core/rnaseq/blob/master/docs/usage.md#transcript-ids-in-fasta-files

<hr />