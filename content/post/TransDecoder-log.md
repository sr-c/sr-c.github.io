---
title: 使用TransDecoder预测转录本的编码区域
date: 2018-04-30 20:57:20
tags: 
---
```TransDecoder```是一款用于预测转录本序列中编码区域的软件。其使用比较简单，下载后其中的perl脚本已预编译完成，可直接使用。
参考官网介绍，分为3步进行。其中第2步可选，将序列比对到```uniprot```或```Pfam```数据库，补充最后的筛选过程。
程序的主要限速步骤在于第2步使用```blast```比对到```uniprot```，可考虑使用```diamond```替代```blast```提升比对速度。
<!-- more -->
根据官网的介绍，其算法标准如下:
1. 找到转录本序列中最小长度的开放读码框(ORF)
2. 以类似于GeneID的方法对序列进行一次打分，得分大于0的序列被保留
3. 若一条ORF的得分高于另外2种正向的ORF，其上述的得分会最高
4. 若一个候选ORF完全被另一个ORF包括，则较长的ORF最终被采纳。需要注意的是，一条转录本序列，能够产生多个ORF（由于操纵子，嵌合体等因素的存在）
5. 位置得分矩阵(PSSM)被用于优化起始密码子的预测
6. （可选）预测的氨基酸序列若与Pfam数据库中功能域的匹配高于其他噪音区域


参考链接：
TransDecoder: https://github.com/TransDecoder/TransDecoder/wiki
PSSM: https://www.ncbi.nlm.nih.gov/Class/Structure/pssm/pssm_viewer.cgi
	https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-6-33
