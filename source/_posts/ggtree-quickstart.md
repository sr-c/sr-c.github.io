---
title: ggtree-quickstart
tags: [ggtree]
date: 2019-04-21 23:44:55
permalink:
categories:
description:
---
<p class="description">使用ggtree绘制系统发育树</p>

<!-- more -->

## 安装

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("treeio", version = "3.8")
BiocManager::install("ggtree", version = "3.8")
```

## 输入

ggtree可以直接文本输入Newick tree

```R
library(ggtree)
tree_text <- "(((((cow, (whale, dolphin)), (pig2, boar)), camel), fish), seedling);"
x <- read.tree(text=tree_text)

```

也可使用`treeio`直接入其他程序的标准输出

```R
library(treeio)
read.r8s()
```

注意，`read.r8s()`一次性读入r8s 标准输出中的3种树，

## 参考来源

https://yulab-smu.github.io/treedata-book/

<hr />