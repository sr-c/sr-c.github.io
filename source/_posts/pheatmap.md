---
title: 使用pheatmap绘制热图
tags: [heatmap]
date: 2018-06-30 11:23:27
permalink:
categories:
description:
---
<p class="description">使用`pheatmap`绘制热图</p>

<!-- more -->

## 安装

```R
source("http://bioconductor.org/biocLite.R")
biocLite("pheatmap")
library(pheatmap)
```

## 读入数据

```R
setwd("path/to/my/workdir/")
data <- read.delim("mydata.txt", header = T, sep = "\t", row.names = 1)
```

## 开始绘制

```R
pheatmap(as.matrix(data), color=colorRampPalette(c("green","black","red"))(100), scale='row', border_color=NA, width=9, height=18, fontsize=9, fontsize_row=6, filename="heatmap.pdf" )
```

## 参数设置

```
scale='row' #对矩阵进行标准化，以row或column方向进行标准化，默认参数为none
cluster_row, cluster_col #默认对行列方向均进行聚类，设置为FALSE可不进行相应方向的聚类
treeheight_row=0, treeheight_col=0 #不显示dendrogram
border_color=NA #热图的每个小块间默认以灰色隔开，设置NA去掉border
legend=FALSE #不显示legend
color=colorRampPalette(rev(c("red","black","green")))(102) #设定cell的颜色
```

### 将数值显示在热图的格子中

`number_format`设置数值的格式，较常用的有 "%.2f"（保留小数点后两位），"%.1e"（科学计数法显示，保留小数点后一位）

`number_color`设置显示内容的颜色

```
pheatmap(test, display_numbers = TRUE, number_format = "%.2f", number_color="purple") #"%.2f"表示保留小数点后两位
# 在热图格子中展示文本
pheatmap(test, display_numbers = TRUE)
pheatmap(test, display_numbers = TRUE, number_format = "\%.1e")
pheatmap(test, display_numbers = matrix(ifelse(test > 5, "*", ""), nrow(test)))#还可以自己设定要显示的内容
```

### 改变每个格子的大小

pheatmap(test, cellwidth = 15, cellheight = 12, main = "Example  heatmap", fontsize = 8, filename = "test.pdf")  

`main`可设置热图的标题，`fontsize`设置字体大小，`filename`可直接将热图存出，支持格式png, pdf, tiff, bmp,  jpeg，并且可以通过`width`, `height`设置图片的大小

```
#pheatmap还可以显示行或列的分组信息，支持多种分组；
annotation_col = data.frame(CellType = factor(rep(c("CT1", "CT2"), 5)), Time = 1:5)
rownames(annotation_col) = paste("Test", 1:10, sep = "")
 
annotation_row = data.frame(GeneClass = factor(rep(c("Path1", "Path2", "Path3"), c(10, 4, 6))))
rownames(annotation_row) = paste("Gene", 1:20, sep = "")
 
pheatmap(test, annotation_col = annotation_col, annotation_row = annotation_row)
```



```
#设定各个分组的颜色
ann_colors = list(Time = c("white", "firebrick"), #连续数值型分组可设置成渐变
    CellType = c(CT1 = "#1B9E77", CT2 = "#D95F02"),
    GeneClass = c(Path1 = "#7570B3", Path2 = "#E7298A", Path3 = "#66A61E"))
pheatmap(test, annotation_col = annotation_col, annotation_row = annotation_row,
         annotation_colors = ann_colors)
```

### 根据特定条件将热图分隔开

`cutree_rows`, `cutree_cols` 根据行列的聚类数将热图分隔开
pheatmap(test, cutree_rows=3, cutree_cols=2)

```
#还可以自己设定各个分组的颜色
ann_colors = list(Time = c("white", "firebrick"), #连续数值型分组可设置成渐变
    CellType = c(CT1 = "#1B9E77", CT2 = "#D95F02"),
    GeneClass = c(Path1 = "#7570B3", Path2 = "#E7298A", Path3 = "#66A61E"))
pheatmap(test, annotation_col = annotation_col, annotation_row = annotation_row, annotation_colors = ann_colors)
#还可以利用gaps_row, gaps_col自己设定要分隔开的位置
pheatmap(test, annotation_col = annotation_col, cluster_rows = FALSE, gaps_row = c(10, 14), cutree_col = 2)
```

## 参考来源

https://blog.csdn.net/sinat_38163598/article/details/72770404

https://cran.r-project.org/web/packages/pheatmap/pheatmap.pdf

<hr />