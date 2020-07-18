---
title: RIdeogram
tags: [R]
date: 2019-01-16 10:06:37
permalink:
categories:
description:
---
<p class="description">使用RIdeogram包绘制染色体图</p>

<!-- more -->

## 安装

```R
install.package("RIdeogram")
# 直接安装出现报错，需要依赖rsvg包
conda install -c conda-forge librsvg r-rsvg
```

## 使用

### 准备输入文件

- karyotype.csv 核型文件，必须。包含5列
  - 第1列 染色体名
  - 第2, 3列 染色体起止位置(0-base)
  - 第4, 5列 着丝粒起止位置(可省略)
- heatmap.csv 染色体heatmap，可选。包含4列
  - 第1列 染色体名
  - 第2, 3列 染色体指定位置
  - 第4列 该位置对应的数值
- mark.csv 染色体旁形状标记，可选。包含6列
  - 第1列 染色体名
  - 第2列 形状(box, triangle, circle)
  - 第3列 染色体名
  - 第4, 5列 染色体指定位置
  - 第6列 颜色

### 绘制

```R
ideogram(karyotype = karyotype, 
         #如果没提供easy_input_heatmap.csv，就在下面两行前面加#
         overlaid = gene_density, 
         #染色体上heatmap的配色
         colorset1 = c("forestgreen", "gold", "deeppink3"), 
         #如果没提供easy_input_mark.csv，就在下面这行前面加#
         label = Random_RNAs_500,
         #染色体宽度，默认170
         width = 200,
         #图例的位置
         Lx = 160, #图例左上角跟左边的距离
         Ly = 20) #图例左上角跟顶端的距离
```

### 格式转换

```R
#svg2tiff, svg2jpg, svg2png函数设定类似
svg2pdf("chromosome.svg",
       width = 12, height = 8, #设定画布大小
       dpi = 300)
```



## 参考来源

https://cran.r-project.org/web/packages/RIdeogram/vignettes/RIdeogram.html

<hr />