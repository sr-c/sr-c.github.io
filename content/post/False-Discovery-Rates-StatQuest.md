---
title: "【StatQuest】错误发现率(FDR)"
date: 2020-08-09T12:45:34+08:00
tags: ["StatQuest", "FDR", "RNA-seq"]
draft: false
---

错误发现率(False Discovery Rates, FDR)在高通量测序的RNA-seq分析中非常常见，甚至还使用过，但是对其中原理还需要更多探究。

## 主旨

> False Discovery Rates are a tool to weed out bad date that looks good.

## 背景

在转录组分析中，我们希望比较不同样品中某个基因的表达量是否存在差异。

假设我们的多次抽样均来自同一个分布。那么各个样本中该基因对应的`read counts`应当符合正态分布。

![same-distribution-frequency](http://www.biotrainee.com/data/attachment/forum/201809/06/172707dmaxukhd4myh7355.jpg)

两次抽样间比较的p值应该在大部分时候(95%)都大于我们的常见的阈值(0.05)。

![same-distribution](https://upload-images.jianshu.io/upload_images/19396348-f5d4fd85e114c3da.png?imageMogr2/auto-orient/strip|imageView2/2)

小概率情况下，我们取样的结果之间存在较大差异，统计分析结果会认为两次抽样分布来自两个不同的分布，即两组样本之间存在显著差异。此时的结果我们称之为”假阳性“(false-positive)。

![false-positive](http://www.biotrainee.com/data/attachment/forum/201809/06/172718aj3a4v7atyyut11a.jpg)



### 理论假设

#### 抽样来自同一个分布

我们假设两次抽样都来自同一个分布，检验两次抽样的结果是否存在显著差异。
![generating-p-values](https://upload-images.jianshu.io/upload_images/19396348-f5d4fd85e114c3da.png?imageMogr2/auto-orient/strip|imageView2/2)
多次重复这一试验过程，然后统计差异检验结果p值的分布。可以发现p值均匀分布在(0, 1)的区间内。那么那些p值落在(0, 0.05)区间的试验结果就是”假阳性“的结果，使得本没有差异的抽样被认为存在差异。
![distribution-of-p-values](https://upload-images.jianshu.io/upload_images/19396348-5952d8dfdc7f3e40.png?imageMogr2/auto-orient/strip|imageView2/2)

#### 抽样来自不同的分布

如果两次抽样确实来自不同的分布，那么大部分假设检验的结果p值都会小于0.05,

![sample-from-diff-distribution](https://upload-images.jianshu.io/upload_images/19396348-f055358c6af8288d.png?imageMogr2/auto-orient/strip|imageView2/2)

统计10000次检验结果p值的分布，可以看到严重偏向于0. 大部分的p值落在(0, 0.05)区间内，而小部分(5%)的p值大于0.05，即”假阴性“，使得本来存在差异的取样被认为来自于同一个分布。

![sample-from-diff-distribution-p-value](https://upload-images.jianshu.io/upload_images/19396348-3ae73e8346e6842c.png?imageMogr2/auto-orient/strip|imageView2/2)

### 实际情况

在转录组分析中，对于大部分真核生物，其基因组中的蛋白编码基因的数量在$$10^4$$水平。

我们假设目标物种存在11000个蛋白编码基因，实验处理（如药物摄入）影响了其中1000个基因的表达量，对另外10000个基因的表达没有影响。

- 对于这10000个未受实验处理影响的蛋白编码基因，其表达量应当来自于同一个分布，但是由于”假阳性“结果的存在，就会有约500个基因被错误地认为表达量存在差异，被归类于差异表达基因；
- 对于那1000个实际收到影响的基因，其中会有约50个基因由于”假阴性“结果的存在，被错误地归类为非差异表达基因。

因此，由于真核生物基因组中蛋白编码基因的基数，导致直接统计检验获得的错误结果较多（550个）。解决方案就是`Benjamini-Hochberg method`, 其中应用到了`FDR`的概念。严格来说，FDR本身并不是一个统计方法，



## Benjamini-Hochberg method 

将两种基因差异检验结果的p值分布综合起来，就可以得到总体上实际的分布情况。我们可以通过肉眼获得来自于表达量未受影响的基因的p值分布（红色虚线），在(0, 0.05)区间内，位于红色虚线上方的蓝色部分那些真阳性的试验结果（差异表达基因）。

> ”假阴性“的差异基因数据较少，在此可忽略。

![distribution-of-all](https://upload-images.jianshu.io/upload_images/19396348-22a950ec367d70d1.png?imageMogr2/auto-orient/strip|imageView2/2)

事实上，对于(0, 0.05)区间内的p值，其分布也是极度偏向0的。因此，我们选择最小的那些p-value作为真阳性的结果。

![ture-positive-p-values](https://upload-images.jianshu.io/upload_images/19396348-fdf06aec8d3cdcc3.png?imageMogr2/auto-orient/strip|imageView2/2)

BH校正方法正是对上述方案的算法实现。校正后获得的`adjusted p value (padjust)`会扩大原来的p值，使得假阳性的p值(<0.05)变得不显著(>0.05)。

首先，将每个样本对应的p值从小到大排列。

然后每个样本的`padjust`取以下两个值中的较小值。

> a: 前一个样本的`padjust`
>
> b: 该基因的p值乘以样本总数与该样本p值排序的商

![BH-adjust](https://upload-images.jianshu.io/upload_images/19396348-fa192a915d48cb61.png?imageMogr2/auto-orient/strip|imageView2/2)

结果使得所有样本的p值都扩大了，原先假阳性样本的p值经过校正后就不再显著了。

![BH-adjust-result](https://upload-images.jianshu.io/upload_images/19396348-6e45a40e77277c0f.png?imageMogr2/auto-orient/strip|imageView2/2)

## 参考来源

https://statquest.org/?s=FDR

https://www.youtube.com/watch?v=K8LQSvtjcEo