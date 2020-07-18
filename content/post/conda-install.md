---
title: 使用conda安装软件
tags: [conda]
date: 2018-06-12 23:17:13
permalink:
categories:
description:
image:
---
<p class="description">使用conda配置环境与安装软件，不失为省时省力的好办法</p>

<!-- more -->

## 安装miniconda

在[软件主页](https://conda.io/miniconda.html)选择需要安装的版本，在此我们选择Python3.6 64位的版本， 下载后运行安装脚本即可完成安装
```bash
sh Miniconda3-latest-Linux-x86_64.sh
```
## 配置conda国内源
```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes 
```
## 配置conda第三方源
### Conda Forge
```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
```
### bioconda
```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
```
## 安装软件
首次安装软件可能会在配置环境时等待较长时间，请耐心等待。。。
```bash
conda install busco
```
## 环境配置
```bash
#新建一个名称为your_env_name的python2.7环境
conda create -n your_env_name python=2.7 
#激活虚拟环境
source activate your_env_name 
#关闭虚拟环境
source deactivate
#删除虚拟环境中的某个包
conda remove --name your_env_name package_name
#删除虚拟环境
conda remove -n your_env_name --all
```
参考来源
https://mirror.tuna.tsinghua.edu.cn/help/anaconda/
<hr />