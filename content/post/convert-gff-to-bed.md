---
title: bed格式转换
tags: [gff, gtf, bed]
date: 2018-10-12 18:21:13
permalink:
categories:
description:
---
<p class="description">将gtf格式文件转换为bed格式</p>

<!-- more -->

## 在线工具

[The Galaxy Project](https://usegalaxy.org/)提供了这方面的[在线工具](https://usegalaxy.org/?tool_id=gff2bed1&version=1.0.1&__identifer=60bjyu3upfp)，可以方便地将gff格式文件转换为bed格式。

但是由于gff格式文件标签太多，导致输出的bed文件可能会确实关键信息（如基因ID）。工具会将以type列作为基因名称，得到错误的ID信息。

使用gtf格式文件转换为bed格式文件能够得到更有用的输出。

## 本地工具

[gtf2Bed.pl](http://blog.sciencenet.cn/home.php?mod=attachment&filename=gtf2Bed.pl&id=62831)

```perl
#!/usr/bin/perl

# Copyright (c) 2011 Erik Aronesty (erik@q32.com)
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
# 
# ALSO, IT WOULD BE NICE IF YOU LET ME KNOW YOU USED IT.

use Data::Dumper;

$in = shift @ARGV;

open IN, ($in =~ /\.gz$/ ? "gunzip -c $in" : $in =~ /\.zip$/ ? "unzip -p $in" : "$in");
while (<IN>) {
	$gff = 2 if /^##gff-version 2/;
	$gff = 3 if /^##gff-version 3/;
	next if /^#/ && $gff;

	s/\s+$//;
	# 0-chr 1-src 2-feat 3-beg 4-end 5-scor 6-dir 7-fram 8-attr
	my @f = split /\t/;
	if ($gff) {
        # most ver 2's stick gene names in the id field
		($id) = $f[8]=~ /\bID="([^"]+)"/;
        # most ver 3's stick unquoted names in the name field
		($id) = $f[8]=~ /\bName=([^";]+)/ if !$id && $gff == 3;
	} else {
		($id) = $f[8]=~ /transcript_id "([^"]+)"/;
	}

	next unless $id && $f[0];

	if ($f[2] eq 'exon') {
		die "no position at exon on line $." if ! $f[3];
        # gff3 puts :\d in exons sometimes
        $id =~ s/:\d+$// if $gff == 3;
		push @{$exons{$id}}, \@f;
		# save lowest start
		$trans{$id} = \@f if !$trans{$id};
	} elsif ($f[2] eq 'start_codon') {
		#optional, output codon start/stop as "thick" region in bed
		$sc{$id}->[0] = $f[3];
	} elsif ($f[2] eq 'stop_codon') {
		$sc{$id}->[1] = $f[4];
	} elsif ($f[2] eq 'miRNA' ) {
		$trans{$id} = \@f if !$trans{$id};
		push @{$exons{$id}}, \@f;
	}
}

for $id ( 
	# sort by chr then pos
	sort {
		$trans{$a}->[0] eq $trans{$b}->[0] ? 
		$trans{$a}->[3] <=> $trans{$b}->[3] : 
		$trans{$a}->[0] cmp $trans{$b}->[0]
	} (keys(%trans)) ) {
		my ($chr, undef, undef, undef, undef, undef, $dir, undef, $attr, undef, $cds, $cde) = @{$trans{$id}};
        my ($cds, $cde);
        ($cds, $cde) = @{$sc{$id}} if $sc{$id};

		# sort by pos
		my @ex = sort {
			$a->[3] <=> $b->[3]
		} @{$exons{$id}};

		my $beg = $ex[0][3];
		my $end = $ex[-1][4];
		
		if ($dir eq '-') {
			# swap
			$tmp=$cds;
			$cds=$cde;
			$cde=$tmp;
			$cds -= 2 if $cds;
			$cde += 2 if $cde;
		}

		# not specified, just use exons
		$cds = $beg if !$cds;
		$cde = $end if !$cde;

		# adjust start for bed
		--$beg; --$cds;
	
		my $exn = @ex;												# exon count
		my $exst = join ",", map {$_->[3]-$beg-1} @ex;				# exon start
		my $exsz = join ",", map {$_->[4]-$_->[3]+1} @ex;			# exon size

		# added an extra comma to make it look exactly like ucsc's beds
		print "$chr\t$beg\t$end\t$id\t0\t$dir\t$cds\t$cde\t0\t$exn\t$exsz,\t$exst,\n";
}


close IN;
```

## 去冗余

由于输入的gff/gtf文件中含有可变剪切信息，转换得到的bed文件会将每个基因的可变剪切列出。如果目标文件中不需要包含可变剪切，只需要对应到基因水平，则还需要进行手动调整。

### 选取目标列

我们只需要目标列`Chrom` `Start` `End` `Name` `Strand`

```bash
# 使用awk，将第4列提前到第2列，并以制表符分隔
awk 'BEGIN {OFS="\t"} {print $1, $4, $2, $3, $6}' old.bed > step1.bed
```

### 删除可变剪切信息

```bash
vi step1.bed
# :%s/\.t.//g
# 删除Name列中基因ID后.t1,.t2,.t3等可变剪切信息
```

### 去重

```bash
sort -k 2 -u step1.bed > final.bed
```

## 参考来源

https://anjingwd.github.io/AnJingwd.github.io/2017/08/15/NGS%E6%95%B0%E6%8D%AE%E6%A0%BC%E5%BC%8FBED-GFF-GTF%E4%B9%8B%E4%BB%8B%E7%BB%8D-%E6%AF%94%E8%BE%83-%E8%BD%AC%E6%8D%A2/

https://lvs071103.gitbooks.io/awk/content/awk_syntax_and_basic_commands/ofs-.html

https://blog.csdn.net/github_36669230/article/details/64503589

https://zhuanlan.zhihu.com/p/20197581

<hr />