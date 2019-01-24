---
title: 使用clusterProfiler进行富集分析
tags: [R, clusterProfiler, AnnotationForge, OrgDB]
date: 2018-12-01 09:26:32
permalink:
categories:
description:
---
<p class="description">使用clusterProfiler对新注释的物种进行富集分析</p>

<!-- more -->

## 构建物种数据库

### 准备注释数据

#### GO注释

由功能注释得到的数据录入，主要分为两类方法：

1. 序列相似性比对（BLAST, diamond）
2. 结构域相似性比对（InterProScan）

待注释序列经由比对，得到公共蛋白数据库（nr, swiss-prot , eggNOG或其他）的ID，而这些ID已经与GO编号相关联（ID对应关系可由[idmapping.tb.gz](ftp://ftp.pir.georgetown.edu/databases/idmapping/idmapping.tb.gz)得到），由此可以得到待注释序列的GO注释。

将基因序列与swiss-prot数据库进行blast/diamond比对（outfmt 6），将结果中的`UniProtKB ID`经由`idmapping.tb.gz`对应到GO编号。

```python
import sys
import gzip
 
USAGE = "\nusage: python %s idmapping.tb.gz blastout outputfile\n" % sys.argv[0]
 
if len(sys.argv) != 4:
    print USAGE
    sys.exit()
 
def parseIDmapping(filename):
    UniProt_GO = {}
    with gzip.open(filename, 'r') as f:
        for line in f:
            lsplit = line.rstrip().split("\t")
            if lsplit[7]:
                UniProt_GO[lsplit[1]] = lsplit[7]
    return UniProt_GO
 
def parseBlastOut(filename):
    tab_res = {}
    with open(filename, 'r') as f:
        for line in f:
            lsplit = line.split()
            tab_res.setdefault(lsplit[0], set()).add(lsplit[1])
    return tab_res
 
 
UniProtKB_GO = parseIDmapping(sys.argv[1])
BlastOut = parseBlastOut(sys.argv[2])
OUT = open(sys.argv[3], 'w')
 
 
for i in BlastOut:
    temp = []
    for j in BlastOut[i]:
        if j in UniProtKB_GO:
            go = UniProtKB_GO[j].split("; ")
            temp = temp + go
        else:
            continue
    if temp:
        OUT.write(i + "\t" +  ",".join(set(temp)) + "\n")
 
OUT.close()
```



#### KEGG注释

KEGG注释可由官方提供的自动注释服务[KAAS](http://www.genome.jp/tools/kaas/)进行，使用blast比对方法，在Gene data set中选择Eukaryotes（真核生物），再加上关注的门类，选择BBH(bi-directional best hit)打分方法，提交注释服务，30~60 分钟即可得到注释结果。

#### eggNOG注释

使用eggNOG-mapper可以得到目标基因序列的注释信息，其中包括GO与KEGG注释，但其注释到的基因数目往往少于直接通过blast序列比对直接得到的结果。

### 清洗注释数据

#### 使用`dplyr`清洗数据

```R
library(tidyverse)
library(stringr)

#读入数据
ko_anno <- read_delim("input/query.ko","\t", escape_double = FALSE, trim_ws = TRUE)
go_anno <- read_delim("input/go_anno.tsv","\t", escape_double = FALSE, trim_ws = TRUE)
emapper <- read_delim("input/sesame.modified.annotations","\t", escape_double = FALSE, trim_ws = TRUE)

# extract gene name from emapper
gene_info <- emapper %>% dplyr::select(query_name = query_name, `eggNOG annot` = `eggNOG annot`) %>% na.omit()

#以KEGG注释列表为基础，合并GO注释与eggNOG注释数据
gene_anno <- dplyr::left_join(ko_anno, go_anno, by = "GID") %>% dplyr::left_join(go_info, by = "GID")
#重命名数据集的列名
names(gene_anno) <- c("query_name","GO_terms","KEGG_KOs","eggNOG annot")
#输出数据集
write.table(gene_anno, file="input/sesame.modified.annotations", sep="\t")
```

### 构建OrgDB

使用`AnnotationForge`构建待注释物种的OrgDB

```R
library(tidyverse)
library(stringr)
library(KEGGREST)
library(AnnotationForge)

#' Title
#'
#' @param f_emapper_anno eggnog-mapper annotation result
#' @param author Who is the creator of this package? like "xxx <xxx@xxx.xx>"
#' @param tax_id The Taxonomy ID that represents your organism. (NCBI has a nice online browser for finding the one you need)
#' @param genus Single string indicating the genus
#' @param species Single string indicating the species
#'
#' @return OrgDb name
#' @export
#'
#' @examples
makeOrgPackageFromEmapper <- function(f_emapper_anno, 
                                      author, 
                                      tax_id = "0", 
                                      genus = "default", 
                                      species = "default") {
  
  # read emapper result
  emapper <- read_delim(f_emapper_anno,
                        "\t", escape_double = FALSE, trim_ws = TRUE)
  
  # extract gene name from emapper
  gene_info <- emapper %>%
    dplyr::select(GID = query_name, GENENAME = `eggNOG annot`) %>%
    na.omit()
  
  # extract go annotation from emapper
  gos <- emapper %>%
    dplyr::select(query_name, GO_terms) %>%
    na.omit()
  
  gene2go = data.frame(GID = character(),
                       GO = character(),
                       EVIDENCE = character())
  
  for (row in 1:nrow(gos)) {
    the_gid <- gos[row, "query_name"][[1]]
    the_gos <- str_split(gos[row,"GO_terms"], ",", simplify = FALSE)[[1]]
    
    df_temp <- data_frame(GID = rep(the_gid, length(the_gos)),
                          GO = the_gos,
                          EVIDENCE = rep("IEA", length(the_gos)))
    gene2go <- rbind(gene2go, df_temp)
  }
  
  # extract kegg pathway annotation from emapper
  gene2ko <- emapper %>%
    dplyr::select(GID = query_name, Ko = KEGG_KOs) %>%
    na.omit()
  
  load(file = "kegg_info.RData")
  gene2pathway <- gene2ko %>% left_join(ko2pathway, by = "Ko") %>% 
    dplyr::select(GID, Pathway) %>%
    na.omit()

  # make OrgDb
  makeOrgPackage(gene_info=gene_info,
                 go=gene2go,
                 ko=gene2ko,
                 pathway=gene2pathway,
                 # gene2pathway=gene2pathway,
                 version="0.0.2",
                 maintainer=author,
                 author=author,
                 outputDir = ".",
                 tax_id=tax_id,
                 genus=genus,
                 species=species,
                 goTable="go")
  
  my_orgdb <- str_c("org.", str_to_upper(str_sub(genus, 1, 1)) , species, ".eg.db", sep = "")
  return(my_orgdb)
}

my_orgdb <- makeOrgPackageFromEmapper("input/sesame.modified.annotations", 
                                      "zhangxudong <zhangxudong@genek.tv>", 
                                      tax_id = "4182", 
                                      genus = "Sesamum", 
                                      species = "indicum")

if (requireNamespace(my_orgdb, quietly = TRUE))
  remove.packages(my_orgdb)
install.packages(my_orgdb, repos = NULL)

library(org.Sindicum.eg.db)
columns(org.Sindicum.eg.db)
```

## 富集分析

```R
library(readr)
library(clusterProfiler)
library(DOSE)

#############################################################################
#OrgDb 是 Bioconductor 中存储不同数据库基因ID之间对应关系，以及基因与GO等注释的对应关系的 R 软件包。
#支持19个物种，可以到这里查询：
#http://bioconductor.org/packages/release/BiocViews.html#___OrgDb
#不支持的物种可以通过 AnnotationForge 自己构建。
#############################################################################
library(org.Sindicum.eg.db)
columns(org.Sindicum.eg.db)

#############################################################################
# 导入需要进行富集分析的基因列表，并转换为向量
#############################################################################
gene_list <- read_csv("input/gene.list", col_names = FALSE)
gene_list <- as.character(gene_list$X1)

#############################################################################
#clusterProfiler 可能是目前最优秀的富集分析软件，参考网站：
#https://www.bioconductor.org/packages/release/bioc/vignettes/clusterProfiler/inst/doc/clusterProfiler.html
#############################################################################

#GO 富集,基于基因数目
ego <- enrichGO(gene          = gene_list, #差异基因 vector
                keyType       = "GID",  #差异基因的 ID 类型，需要是 OrgDb 支持的
                OrgDb         = org.Sindicum.eg.db, #对应的OrgDb
                ont           = "CC", #GO 分类名称，CC BP MF, 也可选择all
                pvalueCutoff  = 1, #Pvalue 阈值，默认值为0.05
                qvalueCutoff  = 1, #qvalue 阈值，默认值为0.2
                pAdjustMethod = "BH", #Pvalue 校正方法
                readable      = FALSE) #TRUE 则展示SYMBOL，FALSE 则展示原来的ID

#将 ego 对象转换为dataframe，新版本可以用as.data.frame(ego)
ego_results<-as.data.frame(ego)
write.table(ego_results, file = "ego_results.txt", quote = F)

```

## 可视化

```R
#以gplot2重新实现的clusterProfiler可视化工具
library(enrichplot)

pdf(file = "ego_barplot.pdf")
barplot(ego, showCategory=20, x = "GeneRatio")
dev.off()

dotplot(ego, showCategory=30)

emapplot(ego)

goplot(ego)

#分面绘图
go <- enrichGO(de, OrgDb = "org.Sindicum.eg.db", ont="all")
dotplot(go, split="ONTOLOGY") + facet_grid(ONTOLOGY~., scale="free")
#Gene-Concept Network
## remove redundent GO terms
ego2 <- simplify(ego)
cnetplot(ego2, foldChange=geneList)
```



## 参考来源

https://zhuanlan.zhihu.com/p/43651419

http://www.genek.tv/course/223

https://mp.weixin.qq.com/s?__biz=MzI1MjU5MjMzNA==&mid=2247486147&idx=1&sn=081fa71939afc04965179d526a5ef018&chksm=e9e02362de97aa7494b581bc33454f6d2cfdf30796fb70a15501b3fe3bcfe68e27b8cdedfc15

https://mp.weixin.qq.com/s?__biz=MzI4NjMxOTA3OA==&mid=2247484154&idx=1&sn=2a992ed151f0a746cdc6997bef9e29ce&chksm=ebdf8a73dca80365ff29497dc73c78f9597e7b739241d15752b178e32b9e05c2ae916a2a3edb&mpshare=1&scene=23&srcid=1113eHRotxw8k1JU2hZomTu7#rd

https://mp.weixin.qq.com/s?__biz=MzI5NjUyNzkxMg==&mid=2247485611&idx=1&sn=69990a569c623730583b56e54d55b58b&scene=21#wechat_redirect

<hr />