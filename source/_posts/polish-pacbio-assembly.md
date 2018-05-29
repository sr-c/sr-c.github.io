---
title: Polish Pacbio Assembly
tags: [pacbio,polish]
date: 2018-05-29 23:18:05
permalink:
categories:
description:
image:
---
<p class="description">Polishing your reference is strongly recomended for the final assembly.</p>

<!-- more -->

In the README of [quickmerge](https://github.com/mahulchak/quickmerge), I got a advice. 

> Assembly polishing with [Quiver](https://github.com/PacificBiosciences/GenomicConsensus) and [pilon](https://github.com/broadinstitute/pilon/wiki) before and after assembly merging is strongly recommended. 

As for `Sequal` data, Pacbio updated the `quiver` to `arrow`.

> Quiver is supported for PacBio RS data. Arrow is supported for PacBio Sequel data and RS data with the P6-C4 chemistry. 

So, let's do it !

## Step 0 Install SMRT Tools

```shell
SMRT_ROOT=/opt/pacbio/smrtlink
./smrtlink_5.1.0.26412.run --rootdir $SMRT_ROOT --smrttools-only
```

SMRT tools will be installed in `/opt/pacbio/smrtlink/smrtcmds/bin` , add this directory into your PATH bash variable. 

## Step 0.5 Convert row `bax` file into `scraps.bam` (RS data)

```shell
bax2bam -f $FOFN -o $BAM_DIR"/SMRT-cell-"$COMPT --subread  --pulsefeatures=DeletionQV,DeletionTag,InsertionQV,IPD,MergeQV,SubstitutionQV,PulseWidth,SubstitutionTag
```

## Step 0.8 Convert `scraps.bam` to `subreads.bam`

## Step 1 Align subreads.bam

```shell
pbalign --nproc 40 $PB_BAM your_assembly.fasta align_xxx.bam
```

## Step 2 Sort bam files

```shell
## Sort alignments by leftmost coordinates, or by read name when -n is used.
samtools sort [-@ threads] xxx.bam -o xxx.sort.bam
```

## Step 3 Merge all the produced bam files

```shell
samtools merge <align_xxx.merge.sort.bam> <in1.bam> <in2.bam> [...]
```

## Step 3.5 Index the input files

```shell
## Index a coordinate-sorted BAM or CRAM file for fast random access
samtools index align_xxx.merge.sort.bam
## Index reference sequence in the FASTA format 
samtools faidx reference.fasta
```

## Step 4 VariantCaller: arrow

```shell
arrow -j 40 --maskRadius 3 \
aligned_xxx.merge.sort.bam \
-r reference.fasta \
-o variants.gff -o myConsesus.fasta -o myConsesus.fastq
```



> **Reference**
>
> https://www.biostars.org/p/273447
>
> https://www.pacb.com/wp-content/uploads/SMRT-Tools-Reference-Guide-v4.0.0.pdf



<hr />