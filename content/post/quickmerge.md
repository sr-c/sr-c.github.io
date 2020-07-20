---
title: quickmerge的使用
tags: [PacBio]
date: 2018-06-04 22:44:59
permalink: [Assembly, Genomics]
categories:
description:
---
<p class="description">quickmerge可以用于综合不同软件的组装结果，以得到更加优秀的参考基因组</p>

<!-- more -->

## install

```bash
wget https://github.com/mahulchak/quickmerge/archive/v0.2.tar.gz
tar zxf v0.2.tar.gz
bash make_merger.sh
```

## Running

The simplest way to run 'merger' is to use the python wrapper 'merge_wrapper_v2.py':

```
 merge_wrapper.py hybrid_assembly.fasta self_assembly.fasta
```

##  Which of my assemblies should I use as query and reference?

There is no golden rule for this. *Quickmerge* was developed  with the idea that every genome is different and that you know best  about the genome you are assembling. All you have to do is to keep in  mind that *quickmerge* joins contigs in one assembly (query  assembly) using the contigs from the reference assembly. So the merged  assembly receives the most sequences from the query assembly, and the  reference assembly provides only the sequences that bridge gaps in the  query assembly. As a result, **the merged assembly size and completeness (as evaluated by BUSCO or CEGMA metric) would be more like the query genome**.  If you believe that completeness and size of the assembly 1 is truer  representation of the actual genome, then you may use that assembly as  the query. Please see below for strategies on one and two rounds of  quickmerge.

## USAGE 1: One round of quickmerge 

Use this when your PacBio assembly (PBcR/canu/Falcon) is much more  fragmented than the hybrid/DBG2OLC assembly. Use your DBG2OLC assembly  as the reference. This is more likely to happen when coverage is low  (<50X). In this case, it is expected that you will run *quickmerge* only once. E.g.

1. Choose your query and reference assembly. You can try one *quickmerge* run with assembly1 as the reference and another *quickmerge* run with assembly1 as the query.
2. Choose all the parameters.
3. Run *quickmerge*. Use the merged assembly with the better N50.

##  USAGE 2: One round merged assembly does not have both best N50 and correct assembly size

This is our preferred method, especially when the PacBio only  assembly is much more contiguous than the DBG2OLC or hybrid assembly  (more likely to happen when coverage is >50X).

1. Choose assembly1 as query and assembly2 as reference.
2. Run *quickmerge* and obtain N50, assembly size, etc.
3. Set assembly2 as query and assembly1 as query.
4. Run *quickmerge* and obtain N50, assembly size, etc.
5. Does either of your merged assemblies (from 2 or 4) give you the best N50 and the correct genome size at the same time?
6. If not, choose the merge assembly that is more contiguous and lets call it assembly3.
7. Lets say that size and other metrics (e.g. BUSCO or CEGMA metric) of assembly1 (or assembly2) is more correct. Now run *quickmerge*  with assembly1 (or assembly2) as the query and assembly3 as the  reference. If you are not using the wrapper python script, you should  rename the contigs in assembly 3 before running *quickmerge*.Simply do`sed -i 's/^>/>N_/g' assembly3.fasta`before running *quickmerge*.
8. Now your merged assembly has the best N50 without sacrificing on the other metrics.



Reference

https://github.com/mahulchak/quickmerge/wiki

<hr />