---
title: 使用KinFin进行比较基因组分析
tags: [KinFin]
date: 2019-03-23 18:31:47
permalink:
categories:
description:
---
<p class="description">使用KinFin整合下游的比较基因组分析</p>

<!-- more -->

## 安装

```bash
git clone https://github.com/DRL/kinfin.git
cd kinfin
./install
```

安装程序会下载一些数据，并使用`pip`安装相应的模块。但若是没有管理员权限，还是使用`conda`来配置环境比较方便。

软件的[环境要求](https://github.com/DRL/kinfin/blob/master/requirements.txt)`python`模块如下

>scipy==0.19.0
>matplotlib==2.0.2
>docopt==0.6.2
>networkx==1.11
>powerlaw==1.4.1
>ete3==3.0.0b35

```bash
conda create -n kinfin python=2.7
source activate kinfin
#matplotlib 2.0.2 与ete3 3.0.0b35的conda环境依赖存在冲突，故直接尝试安装最新版
conda install scipy==0.19.0 matplotlib docopt==0.6.2 networkx==1.11 ete3 
#powerlaw目前只能从第三方源安装
conda install -c mlgill powerlaw 

./kinfin --help
```

出现帮助信息表明软件安装完成

此外，还可以安装一些[其他软件](https://kinfin.readme.io/docs/getting-started#section-additional-useful-software)

```bash
conda install parallel

cd ..
wget https://github.com/gephi/gephi/releases/download/v0.9.2/gephi-0.9.2-linux.tar.gz
tar zxf gephi-0.9.2-linux.tar.gz

# 使用trimAl截短多序列比对后的序列
# http://trimal.cgenomics.org/downloads
wget https://github.com/scapella/trimal/archive/v1.4.1.tar.gz -O trimAl_v1.4.1.tar.gz
tar zxf trimAl_v1.4.1.tar.gz
cd trimal-1.4.1/source
make

git clone https://github.com/PatrickKueck/FASconCAT.git
```

## 测试

KinFin的软件路径中提供了测试数据`./test`，但我们还可以使用[教程](https://kinfin.readme.io/docs/running-the-example-dataset)中的数据来练习。

```bash
# $KINFIN/: path to KinFin installation
# $MAFFT/: path to MAFFT installation
# $TRIMAL/: path to trimal installation
# $FASCONCAT/: path to FASconCAT installation
# $RAXML/: path to RAxML installation

# 1. Clone KinFin manuscript repo
git clone https://github.com/DRL/kinfin_manuscript
cd kinfin_manuscript/

# 2. Basic analysis
tar zxf supplementary_material/supplementary_data_1.tar.gz
$KINFIN/kinfin \
	-g supplementary_data_1/Orthogroups.I1_5.txt \
  -c supplementary_data_1/kinfin.config.basic.txt \
  -s supplementary_data_1/kinfin.SequenceIDs.txt \
  -o kinfin.basic 
  
# 2.1 Get single-copy cluster IDs
grep 'true' kinfin.basic.kinfin_results/all/all.all.cluster_1to1s.txt | \
	cut -f1 > single_copy.cluster_ids.txt

# 2.2 Get protein IDs from single-copy cluster IDs
$KINFIN/scripts/get_protein_ids_from_cluster.py \
	-g supplementary_data_1/Orthogroups.I1_5.txt \
  --cluster_ids single_copy.cluster_ids.txt

# 2.3 Extract FASTA sequences based on protein IDs
tar zxf supplementary_material/supplementary_data_2.tar.gz
cat supplementary_data_2/fastas/*.faa > all.proteins.faa
parallel -j8 '\
	grep --no-group-separator -A1 -wFf {} all.proteins.faa > {/.}.faa\
  ' ::: Orthogroups.I1_5.OG*.txt
  
# 2.4 Align FASTA sequences
parallel -j 8 '\
	$MAFFT/bin/einsi {} > {/.}.einsi.faa' ::: Orthogroup*.faa
 
# 2.5 Trim alignments
parallel -j 8 '\
	$TRIMAL/source/trimal -in {} -out {/.}.trimal.faa -automated1 \
  ' ::: *einsi.faa
  
## ERROR: Alignment not loaded: "Orthogroup.I1_5.OG*.einsi.faa" Check the file's content.

# 2.6 Sanitise headers for FASconCAT
parallel -j 8 '\
	cut -f1 -d"." {} > {/.}.sane.fas \
  ' ::: *trimal.faa

# 2.7 Concatenate alignments
$FASCONCAT/FASconCAT_v1.11.pl -s -p -n

# 2.8 RAxML 注意耗时较长，推荐后台运行
$RAXML/raxmlHPC-PTHREADS-SSE3 -s FcC_smatrix.phy -m PROTGAMMAGTR -T 32 -n ml -N 20 -p 19
$RAXML/raxmlHPC-PTHREADS-SSE3 -m PROTGAMMAGTR -T 48 -n bs -p 19 -b 19 -# 100 -s FcC_smatrix.phy
$RAXML/raxmlHPC-PTHREADS-SSE3 -m GTRCAT -p 19 -f b -t RAxML_bestTree.ml -z RAxML_bootstrap.bs -n final

# 3. Advanced analysis (assuming KinFin executable is in your $PATH)
$KINFIN/kinfin \
	-g supplementary_data_2/Orthogroups.I1_5.txt \
  -c supplementary_data_2/kinfin.config.tree.txt \
  -s supplementary_data_2/kinfin.SequenceIDs.txt \
  -o kinfin.advanced \
  -p supplementary_data_2/kinfin.SpeciesIDs.txt \
  -a supplementary_data_2/fastas/ \
  -t supplementary_data_2/kinfin.tree.nwk \
  -f supplementary_data_2/kinfin.functional_annotation.txt
  
# 4. Infer representative functional annotation (all clusters)
$KINFIN/scripts/filter_functional_annotation_of_clusters.py all \
	-f kinfin.advanced.kinfin_results/cluster_metrics_domains.IPR.txt \
  -c kinfin.advanced.kinfin_results/cluster_counts_by_taxon.txt \
  -x 0.75 \
  -p 0.75 \
  -o kinfin.IPR

# 4. Infer representative functional annotation (synapomorphic clusters)
$KINFIN/scripts/filter_functional_annotation_of_clusters.py synapo \
	-f kinfin.advanced.kinfin_results/cluster_metrics_domains.IPR.txt \
  -c kinfin.advanced.kinfin_results/cluster_counts_by_taxon.txt \
  -t kinfin.advanced.kinfin_results/tree/tree.cluster_metrics.txt \
  -n 0.75 \
  -x 0.75 \
  -p 0.75 \
  -o kinfin.IPR
```



## 参考来源

https://kinfin.readme.io/docs/running-the-example-dataset

<hr />