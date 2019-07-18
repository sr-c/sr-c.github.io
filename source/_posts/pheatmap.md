---
title: 使用pheatmap绘制热图
tags: [heatmap]
date: 2018-06-30 11:23:27
permalink:
categories:
description:
---
<p class="description">使用pheatmap绘制热图</p>
<!-- more -->

## 安装

```R
install.package("pheatmap")
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

### 指定取色范围

默认的取色范围就是数据的取值范围`range(data)`，而常常我们会需求特定的取值范围，或者指定中间特定值（如0）的颜色。那么就需要使用`breaks`参数指定取色范围，并根据break范围设定颜色范围。

```R
#breaks
bk <- c(seq(-9,-0.1,by=0.01),seq(0,9,by=0.01))
#pheatmap
pheatmap(data1,
         scale = "none",
         color = c(colorRampPalette(colors = c("blue","white"))(length(bk)/2),colorRampPalette(colors = c("white","red"))(length(bk)/2)),
         legend_breaks=seq(-8,8,2),
         breaks=bk)
```

![break_color](https://upload-images.jianshu.io/upload_images/903467-20ef5aee33fa32ab.png)

### 设置legend的大小

`pheatmap`关于`legend`仅提供了3个参数，而如何设定legend的大小并不方便。

- legend 是否显示legend
- legend_breaks 以向量方式设定breakpoint
- legend_labels 以向量方式提供breakpoint的标签

一个讨巧的[方法](https://stackoverflow.com/questions/25495965/pheatmap-formatting-in-r-legend-size-and-creating-a-square-plot?answertab=active#tab-top)是通过设定字体的方式来调整，增大字体可以改变legend的大小。而文本的字体可以使用`fontsize_row`和`fontsize_col`来分别限定。

## 参考来源

https://blog.csdn.net/sinat_38163598/article/details/72770404

https://cran.r-project.org/web/packages/pheatmap/pheatmap.pdf

https://www.jianshu.com/p/6b765e83d723

<hr />