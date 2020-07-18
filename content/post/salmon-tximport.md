---
title: Salmon快速转录本定量
tags: [RNA-seq,Salmon,R,tximport]
date: 2019-05-28 22:49:08
permalink:
categories:
description:
---
<p class="description">使用便捷的方式解决问题</p>

<!-- more -->

## Scallop

Scallop可组装出新转录本，作为特定条件下的[补充](https://vidotto.top/post/scallop%E5%92%8Csalmon%E5%BA%94%E7%94%A8%E8%BD%AC%E5%BD%95%E6%9C%AC%E5%AE%9A%E9%87%8F%E5%88%86%E6%9E%90/)。

## Salmon

构建索引

```bash
salmon index -t athal.fa.gz -i athal_index --type quasi -k 31
```

直接定量

```bash
salmon quant -i transcripts_index  -l A -1 reads_1.fastq.gz -2 reads_2.fastq.gz -o transcripts_valid -p 16 --validateMappings
```

结果导入

```R
library(tximport)
txi.salmon <- tximport('quant.sf', type = "salmon", tx2gene = tx2gene)
```

## 多样本处理

### 循环处理

```bash
#!/bin/bash
for fn in ERR1698{194..209};
do    samp=`basename ${fn}`    
echo "Processin sample ${sampe}"   
salmon quant -i athal_index -l A \
        -1 ${samp}_1.fastq.gz \
        -2 ${samp}_2.fastq.gz \
        -p 8 -o quants/${samp}_valid \
        --validateMappings
done
```

### 声明结果文件路径

```R
dir <- "C:/Users/Xu/Desktop/"
list.files(dir)
sample <- paste0("ERR1698",seq(194,209),"_valid")
files <- file.path(dir,"quants",sample,"quant.sf")
names(files) <- paste0("sample",c(1:16))
all(file.exists(files))
```

### 准备转录本与基因的对应关系

```R
# 从公共数据获取
library(AnnotationHub)
ah <- AnnotationHub()
ath <- query(ah,'thaliana')
ath_tx <- ath[['AH52247']]columns(ath_tx)
k <- keys(ath_tx,keytype = "GENEID")
df <- select(ath_tx, keys=k, keytype = "GENEID",columns = "TXNAME")
tx2gene <- df[,2:1] # TXID在前， GENEID在后

# 或自行制备
GENEID <- paste0("ObjectGene",sprintf("%04d", c(1:8946)))
TXNAME <- paste0("ObjectGene",sprintf("%04d", c(1:8946)))
tx2gene <- data.frame(GENEID, TXNAME)
```

### 导入数据

```R
# install.packages("readr")
# install.packages("rsjon")
library("tximport")
library("readr")
txi <- tximport(files, type = "salmon", tx2gene = tx2gene)

names(txi)

head(txi$length)
head(txi$counts)

write.csv(txi$abundance, "salmon_valid_TPM.csv", quote = FALSE)
```

### 计算差异表达

```R
library("DESeq2")
sampleTable <- data.frame(condition = factor(rep(c("Day0","Day1","Day2","Day3"),each=4)))
rownames(sampleTable) <- colnames(txi$counts)
dds <- DESeqDataSetFromTximport(txi, sampleTable, ~condition)
```



## 参考来源

http://www.biotrainee.com/thread-1586-1-1.html

http://www.biotrainee.com/thread-1602-1-1.html

https://bioconductor.org/packages/release/bioc/vignettes/tximport/inst/doc/tximport.html

https://vidotto.top/post/scallop%E5%92%8Csalmon%E5%BA%94%E7%94%A8%E8%BD%AC%E5%BD%95%E6%9C%AC%E5%AE%9A%E9%87%8F%E5%88%86%E6%9E%90/

<hr />