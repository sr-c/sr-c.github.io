---
title: "VESPA_usage"
date: 2020-08-31T20:49:19+08:00
tags: []
categories: []
draft: true
---

VESPA是在大样本数据中进行选择压力分析的流程工具。

<!-- more -->

## 安装

```bash
$ tar -xvzf VESPA.tar.gz
$ cd VESPA
$ chmod +x vespa.py
$ sudo mv vespa.py /usr/local/bin
```

### 通过conda配置运行环境

```bash
cd VESPA
conda env create -f vespa_conda.yml -p /home/user/anaconda3/envs/VESPAenv
```

但是，在目前`1.0.1`版本的文件中，会出现报错

```bash
Solving environment: failed

ResolvePackageNotFound:
  - conda-forge::jpeg==9b=0
```

其中第一个等号后的信息为软件的版本号，第二个等号后的信息为构建的信息`build information`

其实，目前conda-forge的`jpeg`库中最旧的版本即为`9b`，`build information`为`0`，不知为何仍会出现如此报错。

因此我们修改`vespa_conda.yml`中的版本信息至目前最新的版本(linux)

```yaml
- conda-forge::jpeg=9d=h516909a_0
```

再次运行`conda env create -f vespa_conda.yml -p /home/user/anaconda3/envs/VESPAenv`即可将VESPA安装至目标环境中。

之后激活对应环境即可运行VESPA

```bash
source activate VESPAenv
python vespa.py
```

## 运行

### 1. 数据清洗

这一阶段使用两个`BLAST`或`HMMER`从已下载的基因组数据中获取所需的同源蛋白。

#### 使用`clean`或`clean_ensembl`模块清洗输入数据

#### 使用`translate`翻译核酸序列

#### 使用`create_database`将多基因组数据构建至单个数据库

#### 使用`gene_selection`选择特定的基因

#### 其他功能

### 2. 搜寻同源基因

### 多序列比对并重构系统发育关系

### 准备选择压力分析

### 选择压力评估

## 参考来源

https://vespa-evol.readthedocs.io/en/latest/

