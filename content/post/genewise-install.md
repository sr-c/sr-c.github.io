---
title: GeneWise的安装
tags: [debug,install,GeneWise]
date: 2018-09-01 23:01:17
permalink:
categories:
description:
---
<p class="description">GeneWise是用于将蛋白质序列进行同源预测的软件，但安装过程中有许多需要注意的地方。</p>

<!-- more -->

## 软件下载

```bash
wget http://www.ebi.ac.uk/~birney/wise2/wise2.4.1.tar.gz
tar zxf ~/software/wise2.4.1.tar.gz -C ~/opt/
```

## 软件编译

```bashrc
cd /opt/biosoft/wise2.4.1/src/
cd HMMer2
sed 's/getline/getline_new/' sqio.c > a && mv a sqio.c
cd ..
perl -p -i -e 's/isnumber/isdigit/' models/phasemodel.c
make all
echo 'PATH=$PATH:~/opt/wise2.4.1/src/bin/' >> ~/.bashrc
echo 'WISECONFIGDIR=~/opt/wise2.4.1/wisecfg/' >> ~/.bashrc
```

这里修改了两个文件`HMMer2/sqio.c`与`models/phasemodel.c`，否则在编译过程中会有相应报错。

## 注意事项

安装前应判断环境中是否存在`glib`或`glib-2.0`。

若环境中存在glib，则直接按照上述步骤即可完成安装。

若现环境中使用的不是glib，而是glib2，我们可以不安装glib库，修改软件的makefile文件，直接使用glib2编译。但Genewise不止含有一个makefile文件，故通过如下方法进行批量修改。

```bash
find ./ -name makefile | xargs sed -i 's/glib-config/pkg-config --libs glib-2.0/'
export C_INCLUDE_PATH=/usr/include/glib-2.0/:/usr/lib64/glib-2.0/include/:$C_INCLUDE_PATH
```

## 系统版本

```bash
Linux version 2.6.32-696.16.1.el6.centos.plus.x86_64 (mockbuild@c1bl.rdu2.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-18) (GCC) ) #1 SMP Wed Nov 15 18:52:54 UTC 2017
```

## 参考来源

https://jingyan.baidu.com/article/e2284b2b5947e7e2e7118d54.html

http://blog.51cto.com/wjpinrain/1077147

<hr />