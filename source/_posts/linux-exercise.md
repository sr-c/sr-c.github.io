---
title: linux练习题
tags: [linux,生信技能树,biotrainee]
date: 2019-03-11 20:48:07
permalink:
categories:
description:
---
<p class="description">生信技能树的linux练习题</p>

<!-- more -->

http://www.bio-info-trainee.com/2900.html

1. 在任意文件夹下面创建形如 1/2/3/4/5/6/7/8/9 格式的文件夹系列。
```bash
mkdir -p 1/2/3/4/5/6/7/8/9
```
2. 在创建好的文件夹下面，比如我的是 /Users/jimmy/tmp/1/2/3/4/5/6/7/8/9 ，里面创建文本文件 me.txt
```bash
touch me.txt
```
3. 在文本文件 me.txt 里面输入内容:
```bash
cat > me.txt
Go to: http://www.biotrainee.com/
I love bioinfomatics.
And you ?
#Ctrl + D
```
4. 删除上面创建的文件夹 1/2/3/4/5/6/7/8/9 及文本文件 me.txt
```bash
cd ~/tmp/
rm -rf tmp/
```
5. 在任意文件夹下面创建 folder1~5这5个文件夹，然后每个文件夹下面继续创建 folder1~5这5个文件夹
```bash
for m in {1..5}; do
for n in {1..5}; do
mkdir -p folder${m}/folder${n}
done
done
```
6. 在第五题创建的每一个文件夹下面都 创建第二题文本文件 me.txt ，内容也要一样。
```bash
cat > me.txt
Go to: http://www.biotrainee.com/
I love bioinfomatics.
And you ?
#Ctrl + D
for m in {1..5}; do
for n in {1..5}; do
cp me.txt folder${m}/folder${n}
done
done
```
7. 再次删除掉前面几个步骤建立的文件夹及文件
```bash
rm -rf ~/temp
```
8. 下载 http://www.biotrainee.com/jmzeng/igv/test.bed 文件，后在里面选择含有 H3K4me3 的那一行是第几行，该文件总共有几行。
```bash
wget http://www.biotrainee.com/jmzeng/igv/test.bed
# 结果为第8行
grep -n H3K4me3 test.bed
# 结果为10行
wc -l test.bed
```
9. 下载 http://www.biotrainee.com/jmzeng/rmDuplicate.zip 文件，并且解压，查看里面的文件夹结构
```bash
wget http://www.biotrainee.com/jmzeng/rmDuplicate.zip
unzip rmDuplicate.zip
tree rmDuplicate
```
10. 打开第九题解压的文件，进入 rmDuplicate/samtools/single 文件夹里面，查看后缀为 .sam 的文件，搞清楚 生物信息学里面的SAM/BAM 定义是什么
```bash
cd rmDuplicate/samtools/single
less -S tmp.sam
```
11. 安装 samtools 软件
```bash
wget https://github.com/samtools/samtools/releases/download/1.9/samtools-1.9.tar.bz2
tar Jxf samtools-1.9.tar.bz2
```
12. 打开 后缀为BAM 的文件，找到产生该文件的命令。 
```bash
samtools view tmp.rmdup.bam | less -S
cat readme.txt
```
14. 上面的后缀为BAM 的文件的第二列，只有 0 和 16 两个数字，用 cut/sort/uniq等命令统计它们的个数。
```bash
samtools view tmp.rmdup.bam | cut -f 2 | sort | uniq -c
     16 0
     12 16
```
15. 重新打开 rmDuplicate/samtools/paired 文件夹下面的后缀为BAM 的文件，再次查看第二列，并且统计
```bash
cd ../paired
samtools view tmp.rmdup.bam | cut -f 2| sort | uniq -uc
      7 147
      2 163
      1 323
      1 353
      1 371
      1 387
      1 433
      2 83
      2 97
      8 99
```
16. 下载 http://www.biotrainee.com/jmzeng/sickle/sickle-results.zip 文件，并且解压，查看里面的文件夹结构， 这个文件有2.3M，注意留心下载时间及下载速度。
```bash
cd ~/temp
wget http://www.biotrainee.com/jmzeng/sickle/sickle-results.zip
unzip sickle-results.zip
```
17. 解压 sickle-results/single_tmp_fastqc.zip 文件，并且进入解压后的文件夹，找到 fastqc_data.txt 文件，并且搜索该文本文件以 >>开头的有多少行？
```bash
cd sickle-results
unzip single_tmp_fastqc.zip
grep -c '>>' fastqc_data.txt
```
18. 下载 http://www.biotrainee.com/jmzeng/tmp/hg38.tss 文件，去NCBI找到TP53/BRCA1等自己感兴趣的基因对应的 refseq数据库 ID，然后找到它们的hg38.tss 文件的哪一行。
```bash
wget http://www.biotrainee.com/jmzeng/tmp/hg38.tss
grep NM_000546 hg38.tss
```
19. 解析hg38.tss 文件，统计每条染色体的基因个数。
```bash
cut -f 2 hg38.tss | sort | uniq -c
```
20. 解析hg38.tss 文件，统计NM和NR开头的熟练，了解NM和NR开头的含义。
```bash
cut -f 1 hg38.tss | grep -c 'NM'
51064
 cut -f 1 hg38.tss | grep -c 'NR'
15954
# https://en.wikipedia.org/wiki/RefSeq
# NM, mRNA. NR, ncRNA
```

## 参考来源

<hr />