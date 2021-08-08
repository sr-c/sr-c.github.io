---
title: "AGAT使用"
date: 2020-08-28T14:45:13+08:00
tags: [gff, gtf, perl]
categories: ["Genomics"]
draft: false
---

AGAT是`Another Gff Analysis Toolkit`的缩写, 是一个用于操作`GTF/GFF`文件的工具。它集合了一系列perl[脚本](https://github.com/NBISweden/AGAT/wiki#list-of-agat-tools-v021)，并且可以通过`conda`安装。

<!-- more -->

## 环境配置

为了最小化对conda的`base`环境的影响，推荐新建一个`agatEnv`环境用于AGAT的安装。

```bash
conda create -n agatEnv
conda activate agatEnv
conda install -c bioconda agat
```

## 说明事项

其中包含的部分脚本如下

| task                                                         | tool                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **check, fix, pad** missing information into sorted and standardised gff3 | `agat_convert_sp_gxf2gxf.pl`                                 |
| make feature **statistics**                                  | `agat_sp_statistics.pl`                                      |
| make **function statistics**                                 | `agat_sp_functional_statistics.pl`                           |
| **extract** any type of sequence                             | `agat_sp_extract_sequences.pl`                               |
| **extract** attributes                                       | `agat_sp_extract_attributes.pl`                              |
| **complement** annotations (non-overlapping loci)            | `agat_sp_complement_annotations.pl`                          |
| **merge** annotations                                        | `agat_sp_merge_annotations.pl`                               |
| **filter** gene models by ORF size                           | `agat_sp_filter_by_ORF_size.pl`                              |
| **filter** to keep only longest isoforms                     | `agat_sp_keep_longest_isoform.pl`                            |
| **create** introns features                                  | `agat_sp_add_introns.pl`                                     |
| **fix** cds phases                                           | `agat_sp_fix_cds_phases.pl`                                  |
| **manage** IDs                                               | `agat_sp_manage_IDs.pl`                                      |
| **manage** UTRs                                              | `agat_sp_manage_UTRs.pl`                                     |
| **manage** introns                                           | `agat_sp_manage_introns.pl`                                  |
| **manage** functional annotation                             | `agat_sp_manage_functional_annotation.pl`                    |
| **specificity sensitivity**                                  | `agat_sp_sensitivity_specificity.pl`                         |
| **fusion / split** analysis between two annotations          | `agat_sp_compare_two_annotations.pl`                         |
| analyze differences between **BUSCO** results                | `agat_sp_compare_two_BUSCOs.pl`                              |
| convert any **GTF/GFF** into **tabulated format**            | `agat_sp_to_tabulated.pl`                                    |
| convert any **GTF/GFF** into **BED** format                  | `agat_convert_sp_gff2bed.pl`                                 |
| convert any **GTF/GFF** into **GTF** format                  | `agat_convert_sp_gff2gtf.pl`                                 |
| convert any **GTF/GFF** into any **GTF/GFF** (bioperl) format | `agat_convert_sp_gxf2gxf.pl`                                 |
| convert **BED** format into **GFF3** format                  | `agat_convert_bed2gff.pl`                                    |
| convert **EMBL** format into **GFF3** format                 | `agat_convert_embl2gff.pl`                                   |
| convert **genscan** format into **GFF3** format              | `agat_convert_genscan2gff.pl`                                |
| convert **mfannot** format into **GFF3** format              | `agat_convert_mfannot2gff.pl`                                |
| ... and much more ...                                        | ... see [here](https://github.com/NBISweden/AGAT/wiki#list-of-agat-tools-v021) ... |

其中脚本名称中的`_sp_`与`_sq_`具有特殊含义。

`_sp_`代表`SLURP`, 即脚本会一次性全部读入`gff`文件至内存。这样虽然消耗的内存更多，但处理效率更高，也能够修复整个文本中的一些错误。

`_sq_`代表`SEQUENTIAL`, 即脚本会从头至尾逐行读入`gff`文件，而不经过完整性检验。这样的脚本具有相对更高的内存效率。

## 使用

以`agat_convert_sp_gxf2gxf.pl`为例，它能够检测并修复`gtf/gff`文件中的错误与缺失信息，得到排序后符合标准的`gff3`文件。

### example 8 - 只含有CDS信息

```
##gff-version 3
Tob1_contig1	Prodigal:2.60	CDS	476	670	.	-	0	ID=Tob1_00001;locus_tag=Tob1_00001;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	CDS	34266	35222	.	+	0	ID=Tob1_00024;locus_tag=Tob1_00024;product=hypothetical protein
Tob1_contig1	SignalP:4.1	sig_peptide	34266	34298	.	+	0	inference=ab initio prediction:SignalP:4.1;note=predicted cleavage at residue 33;product=putative signal peptide
Tob1_contig1	Prodigal:2.60	CDS	35267	37444	.	-	0	ID=Tob1_00025;locus_tag=Tob1_00025;
Tob1_contig1	SignalP:4.1	sig_peptide	37420	37444	.	-	0	inference=ab initio prediction:SignalP:4.1;note=predicted cleavage at residue 25;product=putative signal peptide
Tob1_contig1	Prodigal:2.60	CDS	38304	39338	.	-	0	ID=Tob1_00026;locus_tag=Tob1_00026;
```

补足第一级(gene)和第二级(mRNA)信息。`agat_convert_sp_gxf2gxf.pl --gff 8_test.gff`:

```
##gff-version 3
Tob1_contig1	Prodigal:2.60	gene	476	670	.	-	0	ID=nbis_NEW-gene-1;locus_tag=Tob1_00001;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	mRNA	476	670	.	-	0	ID=nbis_nol2id-cds-1;Parent=nbis_NEW-gene-1;locus_tag=Tob1_00001;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	exon	476	670	.	-	.	ID=nbis_NEW-exon-1;Parent=nbis_nol2id-cds-1;locus_tag=Tob1_00001;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	CDS	476	670	.	-	0	ID=Tob1_00001;Parent=nbis_nol2id-cds-1;locus_tag=Tob1_00001;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	gene	34266	35222	.	+	0	ID=nbis_NEW-gene-2;locus_tag=Tob1_00024;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	mRNA	34266	35222	.	+	0	ID=nbis_nol2id-cds-2;Parent=nbis_NEW-gene-2;locus_tag=Tob1_00024;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	exon	34266	35222	.	+	.	ID=nbis_NEW-exon-2;Parent=nbis_nol2id-cds-2;locus_tag=Tob1_00024;product=hypothetical protein
Tob1_contig1	Prodigal:2.60	CDS	34266	35222	.	+	0	ID=Tob1_00024;Parent=nbis_nol2id-cds-2;locus_tag=Tob1_00024;product=hypothetical protein
Tob1_contig1	SignalP:4.1	sig_peptide	34266	34298	.	+	0	ID=sig_peptide-1;Parent=nbis_nol2id-cds-2;inference=ab initio prediction:SignalP:4.1;note=predicted cleavage at residue 33;product=putative signal peptide
Tob1_contig1	Prodigal:2.60	gene	35267	37444	.	-	0	ID=nbis_NEW-gene-3;locus_tag=Tob1_00025
Tob1_contig1	Prodigal:2.60	mRNA	35267	37444	.	-	0	ID=nbis_nol2id-cds-3;Parent=nbis_NEW-gene-3;locus_tag=Tob1_00025
Tob1_contig1	Prodigal:2.60	exon	35267	37444	.	-	.	ID=nbis_NEW-exon-3;Parent=nbis_nol2id-cds-3;locus_tag=Tob1_00025
Tob1_contig1	Prodigal:2.60	CDS	35267	37444	.	-	0	ID=Tob1_00025;Parent=nbis_nol2id-cds-3;locus_tag=Tob1_00025
Tob1_contig1	SignalP:4.1	sig_peptide	37420	37444	.	-	0	ID=sig_peptide-2;Parent=nbis_nol2id-cds-3;inference=ab initio prediction:SignalP:4.1;note=predicted cleavage at residue 25;product=putative signal peptide
Tob1_contig1	Prodigal:2.60	gene	38304	39338	.	-	0	ID=nbis_NEW-gene-4;locus_tag=Tob1_00026
Tob1_contig1	Prodigal:2.60	mRNA	38304	39338	.	-	0	ID=nbis_nol2id-cds-4;Parent=nbis_NEW-gene-4;locus_tag=Tob1_00026
Tob1_contig1	Prodigal:2.60	exon	38304	39338	.	-	.	ID=nbis_NEW-exon-4;Parent=nbis_nol2id-cds-4;locus_tag=Tob1_00026
Tob1_contig1	Prodigal:2.60	CDS	38304	39338	.	-	0	ID=Tob1_00026;Parent=nbis_nol2id-cds-4;locus_tag=Tob1_00026
```

### example 9 - 第二级 (mRNA) 与第三级(UTRs)特征信息缺失:

```
##gff-version 3
#!gff-spec-version 1.14
#!source-version NCBI C++ formatter 0.2
##Type DNA NC_003070.9
NC_003070.9	RefSeq	source	1	30427671	.	+	.	organism=Arabidopsis thaliana;mol_type=genomic DNA;db_xref=taxon:3702;chromosome=1;ecotype=Columbia
NC_003070.9	RefSeq	gene	3631	5899	.	+	.	ID=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	exon	3631	3913	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010;
NC_003070.9	RefSeq	exon	3996	4276	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010;
NC_003070.9	RefSeq	exon	4486	4605	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010;
NC_003070.9	RefSeq	exon	4706	5095	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010;
NC_003070.9	RefSeq	exon	5174	5326	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010;
NC_003070.9	RefSeq	exon	5439	5899	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010;
NC_003070.9	RefSeq	CDS	3760	3913	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	CDS	3996	4276	.	+	2	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	CDS	4486	4605	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	CDS	4706	5095	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	CDS	5174	5326	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	CDS	5439	5627	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	start_codon	3760	3762	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
NC_003070.9	RefSeq	stop_codon	5628	5630	.	+	0	ID=NM_099983.2;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010;
```

`agat_convert_sp_gxf2gxf.pl --gff 8_test.gff`:

```
##gff-version 3
#!gff-spec-version 1.14
#!source-version NCBI C++ formatter 0.2
##Type DNA NC_003070.9
NC_003070.9	RefSeq	source	1	30427671	.	+	.	ID=source-1;chromosome=1;db_xref=taxon:3702;ecotype=Columbia;mol_type=genomic DNA;organism=Arabidopsis thaliana
NC_003070.9	RefSeq	gene	3631	5899	.	+	.	ID=nbis_NEW-gene-1;locus_tag=AT1G01010
NC_003070.9	RefSeq	mRNA	3631	5899	.	+	.	ID=NC_003070.9:NAC001;Parent=nbis_NEW-gene-1;locus_tag=AT1G01010
NC_003070.9	RefSeq	exon	3631	3913	.	+	.	ID=NM_099983.2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	exon	3996	4276	.	+	.	ID=nbis_NEW-exon-1;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	exon	4486	4605	.	+	.	ID=nbis_NEW-exon-2;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	exon	4706	5095	.	+	.	ID=nbis_NEW-exon-3;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	exon	5174	5326	.	+	.	ID=nbis_NEW-exon-4;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	exon	5439	5899	.	+	.	ID=nbis_NEW-exon-5;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	CDS	3760	3913	.	+	0	ID=nbis_NEW-cds-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	CDS	3996	4276	.	+	2	ID=nbis_NEW-cds-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	CDS	4486	4605	.	+	0	ID=nbis_NEW-cds-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	CDS	4706	5095	.	+	0	ID=nbis_NEW-cds-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	CDS	5174	5326	.	+	0	ID=nbis_NEW-cds-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	CDS	5439	5627	.	+	0	ID=nbis_NEW-cds-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	five_prime_UTR	3631	3759	.	+	.	ID=nbis_NEW-five_prime_utr-1;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
NC_003070.9	RefSeq	start_codon	3760	3762	.	+	0	ID=nbis_NEW-start_codon-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	stop_codon	5628	5630	.	+	0	ID=nbis_NEW-stop_codon-1;Parent=NC_003070.9:NAC001;locus_tag=AT1G01010
NC_003070.9	RefSeq	three_prime_UTR	5628	5899	.	+	.	ID=nbis_NEW-three_prime_utr-1;Parent=NC_003070.9:NAC001;gbkey=mRNA;locus_tag=AT1G01010
```

### example 18 - 特定基因的相关特征信息未经排序，零散地分布在文件中

```
##gff-version 3
scaffold625	maker	gene	337818	343277	.	+	.	ID=CLUHARG00000005458;Name=TUBB3_2
scaffold625	maker	mRNA	337818	343277	.	+	.	ID=CLUHART00000008717;Parent=CLUHARG00000005458
scaffold625	maker	exon	337818	337971	.	+	.	ID=CLUHART00000008717:exon:1404;Parent=CLUHART00000008717
scaffold625	maker	exon	340733	340841	.	+	.	ID=CLUHART00000008717:exon:1405;Parent=CLUHART00000008717
scaffold789	maker	three_prime_UTR	564589	564780	.	+	.	ID=CLUHART00000006146:three_prime_utr;Parent=CLUHART00000006146
scaffold789	maker	mRNA	558184	564780	.	+	.	ID=CLUHART00000006147;Parent=CLUHARG00000003852
scaffold625	maker	CDS	337915	337971	.	+	0	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	CDS	340733	340841	.	+	0	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	CDS	341518	341628	.	+	2	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	CDS	341964	343033	.	+	2	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	five_prime_UTR	337818	337914	.	+	.	ID=CLUHART00000008717:five_prime_utr;Parent=CLUHART00000008717
scaffold625	maker	three_prime_UTR	343034	343277	.	+	.	ID=CLUHART00000008717:three_prime_utr;Parent=CLUHART00000008717
scaffold789	maker	gene	558184	564780	.	+	.	ID=CLUHARG00000003852;Name=PF11_0240
scaffold789	maker	mRNA	558184	564780	.	+	.	ID=CLUHART00000006146;Parent=CLUHARG00000003852
scaffold789	maker	exon	558184	560123	.	+	.	ID=CLUHART00000006146:exon:995;Parent=CLUHART00000006146
scaffold789	maker	exon	561401	561519	.	+	.	ID=CLUHART00000006146:exon:996;Parent=CLUHART00000006146
scaffold789	maker	exon	564171	564235	.	+	.	ID=CLUHART00000006146:exon:997;Parent=CLUHART00000006146
scaffold789	maker	exon	564372	564780	.	+	.	ID=CLUHART00000006146:exon:998;Parent=CLUHART00000006146
scaffold789	maker	CDS	558191	560123	.	+	0	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	CDS	561401	561519	.	+	2	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold625	maker	exon	341518	341628	.	+	.	ID=CLUHART00000008717:exon:1406;Parent=CLUHART00000008717
scaffold625	maker	exon	341964	343277	.	+	.	ID=CLUHART00000008717:exon:1407;Parent=CLUHART00000008717
scaffold789	maker	CDS	564171	564235	.	+	0	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	CDS	564372	564588	.	+	1	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	five_prime_UTR	558184	558190	.	+	.	ID=CLUHART00000006146:five_prime_utr;Parent=CLUHART00000006146
scaffold789	maker	exon	558184	560123	.	+	.	ID=CLUHART00000006147:exon:997;Parent=CLUHART00000006147
scaffold789	maker	exon	561401	561519	.	+	.	ID=CLUHART00000006147:exon:998;Parent=CLUHART00000006147
scaffold789	maker	exon	562057	562121	.	+	.	ID=CLUHART00000006147:exon:999;Parent=CLUHART00000006147
scaffold789	maker	exon	564372	564780	.	+	.	ID=CLUHART00000006147:exon:1000;Parent=CLUHART00000006147
scaffold789	maker	CDS	558191	560123	.	+	0	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	CDS	561401	561519	.	+	2	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	CDS	562057	562121	.	+	0	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	CDS	564372	564588	.	+	1	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	five_prime_UTR	558184	558190	.	+	.	ID=CLUHART00000006147:five_prime_utr;Parent=CLUHART00000006147
scaffold789	maker	three_prime_UTR	564589	564780	.	+	.	ID=CLUHART00000006147:three_prime_utr;Parent=CLUHART00000006147
```

`agat_convert_sp_gxf2gxf.pl --gff 18_test.gff`:

```
##gff-version 3
scaffold625	maker	gene	337818	343277	.	+	.	ID=CLUHARG00000005458;Name=TUBB3_2
scaffold625	maker	mRNA	337818	343277	.	+	.	ID=CLUHART00000008717;Parent=CLUHARG00000005458
scaffold625	maker	exon	337818	337971	.	+	.	ID=CLUHART00000008717:exon:1404;Parent=CLUHART00000008717
scaffold625	maker	exon	340733	340841	.	+	.	ID=CLUHART00000008717:exon:1405;Parent=CLUHART00000008717
scaffold625	maker	exon	341518	341628	.	+	.	ID=CLUHART00000008717:exon:1406;Parent=CLUHART00000008717
scaffold625	maker	exon	341964	343277	.	+	.	ID=CLUHART00000008717:exon:1407;Parent=CLUHART00000008717
scaffold625	maker	CDS	337915	337971	.	+	0	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	CDS	340733	340841	.	+	0	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	CDS	341518	341628	.	+	2	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	CDS	341964	343033	.	+	2	ID=CLUHART00000008717:cds;Parent=CLUHART00000008717
scaffold625	maker	five_prime_UTR	337818	337914	.	+	.	ID=CLUHART00000008717:five_prime_utr;Parent=CLUHART00000008717
scaffold625	maker	three_prime_UTR	343034	343277	.	+	.	ID=CLUHART00000008717:three_prime_utr;Parent=CLUHART00000008717
scaffold789	maker	gene	558184	564780	.	+	.	ID=CLUHARG00000003852;Name=PF11_0240
scaffold789	maker	mRNA	558184	564780	.	+	.	ID=CLUHART00000006146;Parent=CLUHARG00000003852
scaffold789	maker	exon	558184	560123	.	+	.	ID=CLUHART00000006146:exon:995;Parent=CLUHART00000006146
scaffold789	maker	exon	561401	561519	.	+	.	ID=CLUHART00000006146:exon:996;Parent=CLUHART00000006146
scaffold789	maker	exon	564171	564235	.	+	.	ID=CLUHART00000006146:exon:997;Parent=CLUHART00000006146
scaffold789	maker	exon	564372	564780	.	+	.	ID=CLUHART00000006146:exon:998;Parent=CLUHART00000006146
scaffold789	maker	CDS	558191	560123	.	+	0	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	CDS	561401	561519	.	+	2	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	CDS	564171	564235	.	+	0	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	CDS	564372	564588	.	+	1	ID=CLUHART00000006146:cds;Parent=CLUHART00000006146
scaffold789	maker	five_prime_UTR	558184	558190	.	+	.	ID=CLUHART00000006146:five_prime_utr;Parent=CLUHART00000006146
scaffold789	maker	three_prime_UTR	564589	564780	.	+	.	ID=CLUHART00000006146:three_prime_utr;Parent=CLUHART00000006146
scaffold789	maker	mRNA	558184	564780	.	+	.	ID=CLUHART00000006147;Parent=CLUHARG00000003852
scaffold789	maker	exon	558184	560123	.	+	.	ID=CLUHART00000006147:exon:997;Parent=CLUHART00000006147
scaffold789	maker	exon	561401	561519	.	+	.	ID=CLUHART00000006147:exon:998;Parent=CLUHART00000006147
scaffold789	maker	exon	562057	562121	.	+	.	ID=CLUHART00000006147:exon:999;Parent=CLUHART00000006147
scaffold789	maker	exon	564372	564780	.	+	.	ID=CLUHART00000006147:exon:1000;Parent=CLUHART00000006147
scaffold789	maker	CDS	558191	560123	.	+	0	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	CDS	561401	561519	.	+	2	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	CDS	562057	562121	.	+	0	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	CDS	564372	564588	.	+	1	ID=CLUHART00000006147:cds;Parent=CLUHART00000006147
scaffold789	maker	five_prime_UTR	558184	558190	.	+	.	ID=CLUHART00000006147:five_prime_utr;Parent=CLUHART00000006147
scaffold789	maker	three_prime_UTR	564589	564780	.	+	.	ID=CLUHART00000006147:three_prime_utr;Parent=CLUHART00000006147
```



## 参考来源

https://github.com/NBISweden/AGAT