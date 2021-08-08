---
title: "Reads Mapping More Accuately"
date: 2020-09-02T22:12:31+08:00
tags: [snp, GATK]
categories: []
draft: true
---

获取更准确的比对结果，用于变异分析

<!-- more -->

在`shell`脚本中通过`getopts`传递参数。

```bash
#!/bin/bash
#
#Get more accurately mapping bam files.
#
#Usage:
#  bash accurate_mapping.sh -p [prefix] -r [reference_genome]
#
#
while getopts ":p:r:" opt
do
    case $opt in
        p)
        prefix=$OPTARG
        echo "Sample prefix:$OPTARG"
        ;;
        r)
        reference_genome=$OPTARG
        echo "Reference genome:$OPTARG"
        ;;
        ?)
        echo "未知参数"
        exit 1;;
    esac
done

echo "Step0: Reads quality control.\n$(date '+%Y-%m-%d %H:%M:%S')"
in_fq_1=${prefix}_1.clean.fq.gz
in_fq_2=${prefix}_2.clean.fq.gz
date=$(date +%Y%m%d)
sample_rate=0.01

java -jar /Storage/data002/shurh/opt/Trimmomatic-0.38/trimmomatic-0.38.jar PE -threads 8 \
  ${in_fq_1} ${in_fq_2} \
  ${prefix}.filtered.1.fastq.gz ${prefix}.unpaired.1.fastq.gz \
  ${prefix}.filtered.2.fastq.gz ${prefix}.unpaired.2.fastq.gz \
  ILLUMINACLIP:/Storage/data002/shurh/opt/Trimmomatic-0.38/adapters/TruSeq3-PE-2.fa:2:30:10 \
  LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:50 TOPHRED33
echo "Reads filtered."
date

seqkit sample -p ${sample_rate} -s 123 ${prefix}.filtered.1.fastq.gz -o ${prefix}.sampled.1.fq.gz
seqkit sample -p ${sample_rate} -s 123 ${prefix}.filtered.2.fastq.gz -o ${prefix}.sampled.2.fq.gz
echo "Reads sampled at rate:${sample_rate}."

echo "Step1: Reads mapping with default parameters.\n$(date '+%Y-%m-%d %H:%M:%S')"
bowtie2-build ${reference_genome} genome
bowtie2 -p 4 -x genome -1 ${prefix}.filtered.1.fastq.gz -2 ${prefix}.filtered.2.fastq.gz -S ${prefix}.sam 2> ${prefix}.bowtie2.log
samtools sort -@ 4 -O BAM -o ${prefix}.bam ${prefix}.sam

echo "Step2: Filter the PCR duplicates.\n$(date '+%Y-%m-%d %H:%M:%S')"
java -jar /Storage/data002/shurh/software/picard-2.18.27/picard.jar MarkDuplicates I=${prefix}.bam \
  REMOVE_DUPLICATES=true O=${prefix}.rd.bam M=${prefix}.metrics

java -jar /Storage/data002/shurh/software/picard-2.18.27/picard.jar SamToFastq I=${prefix}.rd.bam \
  F=${prefix}.rd.1.fastq F2=${prefix}.rd.2.fastq

echo "Step3: Error correction with BLESS/musket.\n$(date '+%Y-%m-%d %H:%M:%S')"
#bless -read1 ${prefix}.rd.1.fastq -read2 ${prefix}.rd.2.fastq -kmerlength 21 -prefix ${prefix}
musket ${prefix}.rd.1.fastq ${prefix}.rd.2.fastq -omulti ${prefix} -inorder -p 10

echo "Step4: Reads mapping with strict parameters.\n$(date '+%Y-%m-%d %H:%M:%S')"
## Work with BLESS
#bowtie2 -p 4 -x genome --score-min L,-0.3,-0.3 --rg-id ${prefix} --rg SM:${prefix} --rg PL:ILLUMINA \
#  -1 ${prefix}.1.corrected.fastq -2 ${prefix}.2.corrected.fastq -S ${prefix}.corr.sam 2> ${prefix}.corr.bowtie2.log
## Work with musket
bowtie2 -p 4 -x genome --score-min L,-0.3,-0.3 --rg-id ${prefix} --rg SM:${prefix} --rg PL:ILLUMINA \
  -1 ${prefix}.0 -2 ${prefix}.1 -S ${prefix}.corr.sam 2> ${prefix}.corr.bowtie2.log
samtools sort -@ 4 -O BAM -o ${prefix}.corr.bam ${prefix}.corr.sam

echo "Step5: Refilter the PCR dumplicates.\n$(date '+%Y-%m-%d %H:%M:%S')"
java -jar /Storage/data002/shurh/software/picard-2.18.27/picard.jar MarkDuplicates I=${prefix}.corr.bam \
  REMOVE_DUPLICATES=true O=${prefix}.rd.bam M=${prefix}.metrics
```



## 参考来源

《NGS生物信息分析V6.0 陈连福 郑越》

