---
title: R语言练习题
tags: [R,生信技能树,biotrainee]
date: 2019-03-11 15:52:28
permalink:
categories:
description:
---
<p class="description">生信技能树的R语言练习题</p>

<!-- more -->

## 初级
http://www.bio-info-trainee.com/3793.html
### 1. 打开 Rstudio 告诉我它的工作目录。
```R
getwd()
```
### 2. 新建6个向量，基于不同的原子类型。（重点是字符串，数值，逻辑值）

```R
# Character
v_char <- c("www","biotrainee","com")
# Numeric
v_num <- seq(1,10)
# Logical
v_logical <- c(TRUE, FALSE, FALSE)
# Interger
v_inter <- 2L
# Complex
v_complex <- 2+5i
# Raw
v_raw <- charToRaw("Hello")
```
### 3. 告诉我 getwd() 返回的是什么？
> 当前工作目录

### 4. 新建5个其它数据结构，矩阵，数组，数据框，列表，因子（重点是数据框，矩阵）
```R
# Matrix
matrix1 <- matrix(seq(1,10), nrow = 2, ncol = 5, byrow = TRUE)
# Array
array1 <- array(c(1,0), dim = c(3, 3, 2))
# data frame
data_frame1 <- data.frame(
    gender = c("male", "female", "male"),
    height = c(130, 142, 137),
    age = c(7, 6, 6)
    )
# List
list1 <- list(v_char, v_num, 1)
# Factor
factor1 <- factor(v_char)
```
### 5. 在你新建的数据框进行切片操作，比如首先取第1，3行， 然后取第4，6列
```R
data_frame1[,1:3]
data_frame1[4,6:]
```
### 6. 使用data函数来加载R内置数据集 rivers 描述它。并且可以查看更多的R语言内置的数据集：
```R
data(rivers)
```
### 7. 下载 https://www.ncbi.nlm.nih.gov/sra?term=SRP133642 里面的 RunInfo Table 文件读入到R里面，了解这个数据框，多少列，每一列都是什么属性的元素。

```R
a <- read.table("SraRunTable.txt", header = TRUE, sep = '\t')
str(a)
```
### 8. 下载 https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE111229 里面的样本信息读入到R里面，了解这个数据框，多少列，每一列都是什么属性的元素。
```R
b <- read.table("GSE111229_series_matrix.txt.gz", comment.char = '!', sep = '\t', header = TRUE)
str(b)
```
### 9. 把两个表关联起来，使用merge函数。
```R
c <- merge(a, b, by = "Sample_Name", all.x = TRUE)
```
### 10. 对前面读取的 RunInfo Table 文件在R里面探索其MBases列，包括 箱线图(boxplot)和五分位数(fivenum)，还有频数图(hist)，以及密度图(density) 。
```R
> boxplot(sra_table$MBases)
> fivenum(sra_table$MBases)
[1]  0  8 12 16 74
> hist(sra_table$MBases)
> density(sra_table$MBases)
```
### 11. 把前面读取的样本信息表格的样本名字根据下划线分割看第3列元素的统计情况。第三列代表该样本所在的plate
```R
plate=unlist(lapply(e[,2],function(x){
 x
 strsplit(x,'_')[[1]][3]
}))
```

## 中级

http://www.bio-info-trainee.com/3750.html

### 1. 请根据R包org.Hs.eg.db找到下面ensembl 基因ID 对应的基因名(symbol)
>ENSG00000000003.13
>ENSG00000000005.5
>ENSG00000000419.11
>ENSG00000000457.12
>ENSG00000000460.15
>ENSG00000000938.11

```R
library(org.Hs.eg.db)
g2s=toTable(org.Hs.egSYMBOL)
g2e=toTable(org.Hs.egENSEMBL)
g2s_e <- merge(g2s, g2e, by = 'gene_id', all = TRUE)
esbID <- c("ENSG00000000003.13",
+             "ENSG00000000005.5",
+             "ENSG00000000419.11",
+             "ENSG00000000457.12",
+             "ENSG00000000460.15",
+             "ENSG00000000938.11")
# 使用strsplit处理文本ID，取.前的部分
ID6 <- unlist(lapply(esbID, function(x){
 strsplit(x,'.',fixed = TRUE)[[1]][1]
 })
)
ID6 <- data.frame(ensembl_id = ID6)
merge(ID6, g2s_e, by = "ensembl_id", all.x = T)
```
### 2. 根据R包hgu133a.db找到下面探针对应的基因名(symbol)
```R
rm(list = ls())
options(stringsAsFactors = F)
library(hgu133a.db)
ids=toTable(hgu133aSYMBOL)
pr_id <- read.table("R_test2.txt", col.names = c("probe_id"))
merge(pr_id, ids, by = "probe_id", all.x = F)
```
### 3. 找到R包CLL内置的数据集的表达矩阵里面的TP53基因的表达量，并且绘制在 progres.-stable分组的boxplot图
```R
rm(list = ls())
options(stringsAsFactors = F)
suppressPackageStartupMessages(library(CLL))
data(sCLLex)
exprSet=exprs(sCLLex) 
library(hgu95av2.db)
columns(hgu95av2.db)
TP53_prob <- select(hgu95av2.db, 
       keys = c("TP53"),
       columns = c("ALIAS","PROBEID"),
       keytype = "ALIAS")
boxplot(t(exprSet[TP53_prob$PROBEID,]))
boxplot(exprSet[1,]~gourp_list)
```
### 4. 找到BRCA1基因在TCGA数据库的乳腺癌数据集(Breast Invasive Carcinoma (TCGA, PanCancer Atlas))的表达情况
```R
# http://www.cbioportal.org/index.do
# Select Studies 选项中输入Breast Invasive Carcinoma PanCancer 搜索并选中目标数据集
# Enter Genes 数据框中输入目标基因BRCA1
# 点击Submit Query提交搜索请求
# 结果页面中，Plots选项卡选择合适数据进行绘图，或下载数据
```
### 5. 找到TP53基因在TCGA数据库的乳腺癌数据集的表达量分组看其是否影响生存
> http://www.oncolnc.org/kaplan/?lower=50&upper=50&cancer=BRCA&gene_id=7157&raw=TP53&species=mRNA
### 6. 下载数据集GSE17215的表达矩阵并且提取下面的基因画热图

> ACTR3B ANLN BAG1 BCL2 BIRC5 BLVRA CCNB1 CCNE1 CDC20 CDC6 CDCA1 CDH3 CENPF CEP55 CXXC5 EGFR ERBB2 ESR1 EXO1 FGFR4 FOXA1 FOXC1 GPR160 GRB7 KIF2C KNTC2 KRT14 KRT17 KRT5 MAPT MDM2 MELK MIA MKI67 MLPH MMP11 MYBL2 MYC NAT1 ORC6L PGR PHGDH PTTG1 RRM2 SFRP1 SLC39A6 TMEM45B TYMS UBE2C UBE2T

```R
# 下载链接ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE17nnn/GSE17215/matrix/GSE17215_series_matrix.txt.gz
library(hthgu133a.db)
expMa <- read.table("zhong6/GSE17215_series_matrix.txt.gz",
                    comment.char = "!",
                    sep = "\t",
                    header = TRUE,
                    row.names = "ID_REF")
ALIAS <- "ACTR3B ANLN BAG1 BCL2 BIRC5 BLVRA CCNB1 CCNE1 CDC20 CDC6 CDCA1 CDH3 CENPF CEP55 CXXC5 EGFR ERBB2 ESR1 EXO1 FGFR4 FOXA1 FOXC1 GPR160 GRB7 KIF2C KNTC2 KRT14 KRT17 KRT5 MAPT MDM2 MELK MIA MKI67 MLPH MMP11 MYBL2 MYC NAT1 ORC6L PGR PHGDH PTTG1 RRM2 SFRP1 SLC39A6 TMEM45B TYMS UBE2C UBE2T"
ALIAS_id <- strsplit(ALIAS, split = " ")[[1]]
probe_id <- select(hthgu133a.db, 
                    keys = ALIAS_id,
                    columns = c("ALIAS","PROBEID"),
                    keytype = "ALIAS")
names(probe_id) <- c("ALIAS","ID_REF")
exprSet <- merge(probe_id, expMa, by = "ID_REF")[,-2]
row.names(exprSet) <- exprSet$ID_REF
exprSet <- exprSet[,-1]
exprSet <- log(edgeR::cpm(exprSet) + 1)
pheatmap::pheatmap(log(exprSet + 1))
```
### 7. 下载数据集GSE24673的表达矩阵计算样本的相关性并且绘制热图，需要标记上样本分组信息
```R
expMa <- read.table("zhong7/GSE24673_series_matrix.txt.gz",
                    comment.char = "!",
                    sep = "\t",
                    header = TRUE,
                    row.names = "ID_REF")
dim(expMa)
group_list <- factor(c(rep("RB_139_09",3),rep("RB_535_09",3), rep("RB_984_09",3), rep("Healthy", 2)))
# pheatmap
choosen_gene <- expMa[names(sort(apply(expMa,1,mad),decreasing = TRUE))[1:50],]
heatMatrix <- t(scale(t(choosen_gene)))
annotation_col <- data.frame(group_list=group_list)
rownames(annotation_col) <- colnames(choosen_gene)
pheatmap::pheatmap(heatMatrix, annotation_col = annotation_col)
```
### 8. 找到 GPL6244 platform of Affymetrix Human Gene 1.0 ST Array 对应的R的bioconductor注释包，并且安装它！
```R
options(BioC_mirror="https://mirrors.ustc.edu.cn/bioc/")
options("repos" = c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/"))
BiocManager::install("hugene10sttranscriptcluster.db", version = "3.8")
# 直接根据关键字搜索，第一个结果可能是pd.hugene.1.0.st.v1, 但它并不是GPL6244正确的注释包。
# 关于芯片与注释包的对应关系，参考jimmy同学的文章https://www.jianshu.com/p/f6906ba703a0
```
### 9. 下载数据集GSE42872的表达矩阵，并且分别挑选出 所有样本的(平均表达量/sd/mad/)最大的探针，并且找到它们对应的基因。
```R
library(GEOquery)
gset = getGEO('GSE42872', GSEMatrix = TRUE, AnnotGPL = FALSE, getGPL= FALSE)
exprSet = exprs(gset[[1]])
# exprSet <- exprSet[apply(exprSet, 1, function(x) sum(x>1) > 5),]
# exprSet <- log(edgeR::cpm(exprSet) + 1)
# 处理中平均值最大的探针
max_mean <- names(sort(apply(exprSet,1, mean), decreasing = T))[1]
# 处理中sd最大的探针
max_sd <- names(sort(apply(exprSet,1, sd), decreasing = T))[1]
# 处理中mad最大的探针
max_mad <- names(sort(apply(exprSet,1, mad), decreasing = T))[1]
# 查询探针ID对应基因的symbol
library(hugene10sttranscriptcluster.db)
select(hugene10sttranscriptcluster.db,
       keys = c(max_mean, max_sd, max_mad),
       columns = c("SYMBOL"),
       keytype = "PROBEID")
```
### 10. 下载数据集GSE42872的表达矩阵，并且根据分组使用limma做差异分析，得到差异结果矩阵
```R
gset = getGEO('GSE42872', GSEMatrix = TRUE, AnnotGPL = FALSE, getGPL= FALSE)
s <- toTable(hugene10sttranscriptclusterSYMBOL)
exprSet = exprs(gset[[1]])
group_list <- as.character(c(rep("Control",3),rep("Vemurafenib",3)))
dim(exprSet)
# 过滤【只保留和注释文件探针id相同的探针】
efilt <- exprSet[rownames(exprSet)%in%s$probe_id,]
dim(efilt)
## 整合1【目的：保证一个基因对应一个探针；如果基因和探针一一对应很好说，但如果一个基因对应多个探针：每个探针取一行的均值-》对应同一基因的探针取表达量最大的探针-》按照基因名给他们建索引，因为是按照基因来过滤探针（不用s$probe_id构建索引的原因是，看清楚我们的目的是让注释包的一个基因对应我们自己表达矩阵的一个探针。如果用s$probe_id那么结果就成了让注释包的一个探针对应我们自己表达矩阵的一个探针，当然这样也运行不成功，因为自己表达矩阵的探针过滤后的数量和注释包的探针数量不相等，这样没法一一对应。但基因名数量是不变的，什么是索引？以不变应万变的就是索引）】
maxp = by(efilt,s$symbol,function(x) rownames(x)[which.max(rowMeans(x))]) 
uniprobes = as.character(maxp)
efilt=efilt[rownames(efilt)%in%uniprobes,]
## 整合2【目的：将我们表达矩阵的行名换成刚才一对一的基因名，并且match这个函数保证了表达矩阵和注释包的顺序是一致的】
rownames(efilt)=s[match(rownames(efilt),s$probe_id),2]

# 差异分析
suppressMessages(library(limma))
#limma需要三个矩阵：表达矩阵（efilt）、分组矩阵（design）、比较矩阵（contrast）
#先做一个分组矩阵～design，说明MAO是哪几个样本，MNO又是哪几个，其中1代表“是”
design <- model.matrix(~0+factor(group_list))
colnames(design) <- levels(factor(group_list))
rownames(design) <- colnames(efilt)
design
#再做一个比较矩阵【一般是case比control】
contrast<-makeContrasts(paste0(unique(group_list),collapse = "-"),levels = design)
contrast

DEG <- function(efilt,design,contrast){
  ##step1
  fit <- lmFit(efilt,design)
  ##step2
  fit2 <- contrasts.fit(fit, contrast) 
  fit2 <- eBayes(fit2)  
  ##step3
  mtx = topTable(fit2, coef=1, n=Inf)
  deg_mtx = na.omit(mtx) 
  return(deg_mtx)
}
DEG_mtx <- DEG(efilt,design,contrast) #得到全部的差异基因矩阵
```

## 高级

http://www.bio-info-trainee.com/3409.html

### 1.安装一些R包：
```
数据包： ALL, CLL, pasilla, airway 
软件包：limma，DESeq2，clusterProfiler 
工具包：reshape2
绘图包：ggplot2
```

```R
BiocManager::install("reshape2", version = "3.8")
```

### 2. 了解ExpressionSet对象，比如`CLL`包里面就有`data(sCLLex)` ，找到它包含的元素，提取其表达矩阵(使用exprs函数)，查看其大小

```R
> library(CLL)
# sCLLex是一个ExpressionSet对象，封装了表达矩阵与样本信息
> data(sCLLex)
> sCLLex
ExpressionSet (storageMode: lockedEnvironment) #ExpressionSet对象
assayData: 12625 features, 22 samples 
  element names: exprs 
protocolData: none
phenoData
  sampleNames: CLL11.CEL CLL12.CEL ... CLL9.CEL (22 total) #样品名称
  varLabels: SampleID Disease #用于分组的变量
  varMetadata: labelDescription
featureData: none
experimentData: use 'experimentData(object)'
Annotation: hgu95av2 #对应的注释包
# 通过exprs函数提取表达矩阵
> exprMatrix <- exprs(sCLLex)
> dim(exprMatrix)
# 通过pData函数提取样本分组信息
> meta <- pData(sCLLex)
> table(meta$Disease)
```

### 3. 了解 str,head,help函数，作用于第二步提取到的表达矩阵 

```R
> str(exprMatrix) #结果表明这是一个12625行，22列的二维矩阵
 num [1:12625, 1:22] 5.74 2.29 3.31 1.09 7.54 ...
 - attr(*, "dimnames")=List of 2
  ..$ : chr [1:12625] "1000_at" "1001_at" "1002_f_at" "1003_s_at" ...
  ..$ : chr [1:22] "CLL11.CEL" "CLL12.CEL" "CLL13.CEL" "CLL14.CEL" ...
> head(exprMatrix) #得到前6行结果
> help(exprMatrix) #该函数已过时。The function, class, or data object you have asked for has been deprecated or made defunct.
```

### 4. 安装并了解 `hgu95av2.db` 包,看看 `ls("package:hgu95av2.db")` 后 显示的那些变量

```R
> BiocManager::install("hgu95av2.db", version = "3.8")
> library(hgu95av2.db)
> ls("package:hgu95av2.db") #可以发现hgu95v2.db中含有24个变量
 [1] "hgu95av2"              "hgu95av2.db"          
 [3] "hgu95av2_dbconn"       "hgu95av2_dbfile"      
 [5] "hgu95av2_dbInfo"       "hgu95av2_dbschema"    
 [7] "hgu95av2ACCNUM"        "hgu95av2ALIAS2PROBE"  
 [9] "hgu95av2CHR"           "hgu95av2CHRLENGTHS"   
[11] "hgu95av2CHRLOC"        "hgu95av2CHRLOCEND"    
[13] "hgu95av2ENSEMBL"       "hgu95av2ENSEMBL2PROBE"
[15] "hgu95av2ENTREZID"      "hgu95av2ENZYME"       
[17] "hgu95av2ENZYME2PROBE"  "hgu95av2GENENAME"     
[19] "hgu95av2GO"            "hgu95av2GO2ALLPROBES" 
[21] "hgu95av2GO2PROBE"      "hgu95av2MAP"          
[23] "hgu95av2MAPCOUNTS"     "hgu95av2OMIM"     
```

### 5. 理解 `head(toTable(hgu95av2SYMBOL))` 的用法，找到 TP53 基因对应的探针ID

```R
> head(toTable(hgu95av2SYMBOL)) #查看芯片ID与基因symbol之间的对应关系
   probe_id  symbol
1   1000_at   MAPK3
2   1001_at    TIE1
3 1002_f_at CYP2C19
4 1003_s_at   CXCR5
5   1004_at   CXCR5
6   1005_at   DUSP1
> probe_sym <- toTable(hgu95av2SYMBOL)
> subset(probe_sym, symbol == "TP53") #使用subset抽取data.frame中的数据，筛选条件可以多样，使用逻辑关系运算组合
      probe_id symbol
966    1939_at   TP53
997  1974_s_at   TP53
1420  31618_at   TP53
```

### 6. 理解探针与基因的对应关系，总共多少个基因，基因最多对应多少个探针，是哪些基因，是不是因为这些基因很长，所以在其上面设计多个探针呢？

```R
> library(magrittr)
> probe_sym$symbol%>%unique()%>%length() #基因的数目
[1] 8585
> probe_sym$symbol%>%table()%>%sort()%>%tail() #对应最多探针的基因有哪些
.
YME1L1  GAPDH INPP4A    MYB PTGER3  STAT1 
     7      8      8      8      8      8 
```

### 7. 第二步提取到的表达矩阵是12625个探针在22个样本的表达量矩阵，找到那些不在 `hgu95av2.db` 包收录的对应着SYMBOL的探针。

```R
e_filter_out <- exprMatrix[!rownames(exprMatrix)%in%probe_sym$probe_id,]
```

### 8. 过滤表达矩阵，删除那1165个没有对应基因名字的探针。

```R
e_filter <- exprMatrix[rownames(exprMatrix)%in%probe_sym$probe_id,]
```

### 9. 整合表达矩阵，多个探针对应一个基因的情况下，只保留在所有样本里面平均表达量最大的那个探针。

```R
max_e <- by(e_filter, 
            probe_sym$symbol, 
            function(x) rownames(x)[which.max(rowMeans(x))])
uniprobes <- as.character(max_e)
e_filter <- e_filter[rownames(e_filter)%in%uniprobes,]
```

### 10. 把过滤后的表达矩阵更改行名为基因的symbol，因为这个时候探针和基因是一对一关系了。

```R
rownames(e_filter) <- probe_sym[match(rownames(e_filter), probe_sym$probe_id),2]
```

### 11. 对第10步得到的表达矩阵进行探索，先画第一个样本的所有基因的表达量的boxplot,hist,density ， 然后画所有样本的 这些图

```R
exprM_L1 <- data.frame(symbol = row.names(e_filter),
                       expm = e_filter[,1])
p_box <- ggplot(exprM_L1, aes(y = expm)) + geom_boxplot()
p_hist <- ggplot(exprM_L1, aes(x = expm)) + geom_histogram(bins = 50)
p_dens <- ggplot(exprM_L1, aes(x = expm)) + geom_density()
```

### 12. 理解统计学指标mean,median,max,min,sd,var,mad并计算出每个基因在所有样本的这些统计学指标，最后按照mad值排序，取top 50 mad值的基因，得到列表。

> 注意：这个题目出的并不合规，请仔细看。

```R
e_mean <- rowMeans(e_filter)
e_median <- rowMedians(e_filter)
e_max <- rowMax(e_filter)
e_min <- rowMin(e_filter)
e_sd <- apply(e_filter, 1, sd) %>% sort() #没有对应的row系列函数的话，可以使用apply方法
e_var <- apply(e_filter, 1, var) %>% sort() 
e_mad_50 <- apply(e_filter, 1, mad) %>% sort() %>% tail(50)
> names(e_mad_50)
 [1] "PFN2"     "TNFSF9"   "ARHGAP44" "P2RY14"   "THEMIS2"  "LPL"     
 [7] "ANXA4"    "DUSP6"    "DUSP5"    "H1FX"     "FLNA"     "CLEC2B"  
[13] "TSPYL2"   "ZNF266"   "S100A9"   "NR4A2"    "TGFBI"    "ARF6"    
[19] "APBB2"    "VCAN"     "RBM38"    "CAPG"     "PLXNC1"   "RGS2"    
[25] "RNASE6"   "VAMP5"    "CYBB"     "GNLY"     "CCL3"     "OAS1"    
[31] "TRIB2"    "ZNF804A"  "IGH"      "PCDH9"    "VIPR1"    "COBLL1"  
[37] "GUSBP11"  "S100A8"   "HBB"      "LHFPL2"   "FCN1"     "ZAP70"   
[43] "IGLC1"    "LGALS1"   "FOS"      "SLAMF1"   "TCF7"     "DMD"     
[49] "IGF2BP3"  "FAM30A"  
```

### 13. 根据第12步骤得到top 50 mad值的基因列表来取表达矩阵的子集，并且热图可视化子表达矩阵。试试看其它5种热图的包的不同效果。

```R
e50 <- e_filter[names(e_mad_50),]
pheatmap::pheatmap(e50)
tt_50 <- t(scale(t(e50)))
pheatmap::pheatmap(tt_50)
```

### 14. 取不同统计学指标mean,median,max,mean,sd,var,mad的各top50基因列表，使用UpSetR包来看他们之间的overlap情况。

```R
e_mean_50 <- rowMeans(e_filter) %>% sort() %>% tail(50)
e_median_50 <- rowMedians(e_filter) %>% sort() %>% tail(50)
e_max_50 <- rowMax(e_filter) %>% sort() %>% tail(50)
e_min_50 <- rowMin(e_filter) %>% sort() %>% tail(50)
e_sd_50 <- apply(e_filter, 1, sd) %>% sort() %>% tail(50)
e_var_50 <- apply(e_filter, 1, var) %>% sort() %>% tail(50)
e_mad_50 <- apply(e_filter, 1, mad) %>% sort() %>% tail(50)

g_all <- unique(c(names(e_mean_50),
                  names(e_median_50),
                  names(e_max_50),
                  names(e_min_50),
                  names(e_sd_50),
                  names(e_var_50),
                  names(e_mad_50)))
g_upset <- data.frame(g_all = g_all,
                      g_mean=ifelse(g_all %in% names(e_mean_50),1,0),
                      g_median=ifelse(g_all %in% names(e_median_50),1,0),
                      g_max=ifelse(g_all %in% names(e_max_50),1,0),
                      g_min=ifelse(g_all %in% names(e_min_50),1,0),
                      g_sd=ifelse(g_all %in% names(e_sd_50),1,0),
                      g_var=ifelse(g_all %in% names(e_var_50),1,0),
                      g_mad=ifelse(g_all %in% names(e_mad_50),1,0)
                      )
UpSetR::upset(g_upset, nsets = 7)
```

### 15. 在第二步的基础上面提取`CLL`包里面的`data(sCLLex)` 数据对象的样本的表型数据。

```R
pdata=pData(sCLLex)
group_list=as.character(pdata[,2])
group_list
```

### 16. 对所有样本的表达矩阵进行聚类并且绘图，然后添加样本的临床表型数据信息(更改样本名)




## 参考来源

https://www.jianshu.com/p/993b4022363f

http://www.bio-info-trainee.com/3415.html

https://www.statmethods.net/stats/withby.html

<hr />