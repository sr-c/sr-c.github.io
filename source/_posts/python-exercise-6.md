---
title: Python练习6
tags: [python, rosalind]
date: 2019-02-22 11:17:06
permalink:
categories:
description:
---
<p class="description">在Rosalind中练习python</p>

<!-- more -->

## Overlap Graphs

http://rosalind.info/problems/grph/

基于图论的基本设定，二代测序的组装就是基于kmer的延伸

```python
import sys
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('--input','-i',
                    type=str,
                    required=True,
                    help='input file in fasta format')
args = parser.parse_args()


def read_fasta(input):
    with open(input) as read_fasta:
        fasta = {}
        for line in read_fasta:
            line = line.strip()
            if line[0] == '>':
                header = line[1:]
            else:
                sequence = line
                fasta[header] = fasta.get(header, '') + sequence
    return fasta

#取得序列的suffix
def suffix(str):
    sub = []
    for i in range(len(str)-3,len(str)):
        sub.append(str[i])
    return sub

#取得序列的prefix
def prefix(str):
    sub = []
    for i in range(3):
        sub.append(str[i])
    return sub

fasta = read_fasta(args.input)

#print(len(fasta.keys()))
for i in fasta.keys():
    for k in fasta.keys():
        if suffix(fasta[i]) == prefix(fasta[k]) and i != k:
            print("{} {}".format(i,k))
```

### 他山之石

```python
#!/usr/bin/env python
from collections import defaultdict
from rosalind import readfasta

def overlap(alist):
    prefixes = defaultdict(list)
    for name, code in alist:
        prefixes[code[:3]].append(name)
    for name, code in alist:
        suffix = code[-3:]
        for other in prefixes[suffix]:
            if other != name:
                print name, other

overlap(readfasta())
```

## Finding a Protein Motif

http://rosalind.info/problems/mprt/

```python
import requests

with open("rosalind_mprt.txt") as f:
    ID_list = f.readlines()

output = {}
for pepID in ID_list:
    pepID = pepID.strip()
    pep_fasta = requests.get("https://www.uniprot.org/uniprot/" + pepID + ".fasta").text
#print(test)
    lines = pep_fasta.splitlines()
#fasta = {}
    body = ''
    for line in lines:
        if line[0] == '>':
            header = line[1:]
        else:
            body += line

    print(body)
    local = []
    for i in range(len(body)-4):
        kmer = body[i:i+4]
        if kmer[0] == 'N' \
                and kmer[1] != 'P' \
                and kmer[3] != 'P' \
                and (kmer[2] == "S" or kmer[2] == "T"):
            local.append(str(i+1))

    if local != []:
        output[pepID] = local

# for i in output.keys():
#     print(i + '\n' + ' '.join(output[i]))
with open("rosalind_mprt.out",'w') as o:
    for i in output.keys():
        o.write(i + '\n' + ' '.join(output[i]) + '\n')
```

### 他山之石

```python
from re import finditer
from sys import argv
from urllib.request import urlopen

uniprot = "http://www.uniprot.org/uniprot/%s.fasta"

for protein in open(argv[1], 'r').read().strip().splitlines():

    # Fetch the protein's fasta file and get rid of newlines.'
    f = urlopen(uniprot % protein).read().decode('utf-8')
    f = ''.join(f.splitlines()[1:])

    # Find all the positions of the N-glycosylation motif.
    locs = [g.start()+1 for g in finditer(r'(?=N[^P][ST][^P])', f)]

    # Print them out, if any.
    if locs != []:
        print(protein)
        print(' '.join(map(str, locs)))
```

## Consensus and Profile

http://rosalind.info/problems/cons/

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('--input','-i',
                    type=str,
                    required=True,
                    help="input file in fasta format with equal length strings")
parser.add_argument('--output','-o',
                    type=str,
                    default="rosalind_cons.out",
                    help="output file")
args = parser.parse_args()


def read_fasta(input):
    fasta = {}
    with open(input) as f:
        lines = f.readlines()
        for line in lines:
            line = line.strip()
            if line[0] == '>':
                seqID = line[1:]
            else:
                fasta[seqID] = fasta.get(seqID, '') + line
    return fasta


def get_line(N):
    for i in fasta.keys():
        seq_length = len(fasta[i])
    list_a = [0 for x in range(seq_length)]
    for i in fasta.keys():
        for k in range(seq_length):
            if fasta[i][k] == str(N):
                list_a[k] += 1
    return list_a

fasta = read_fasta(args.input)
bases = ['A','C','G','T']
seq_length = len(get_line('A'))
consensus = ['']*seq_length
for m in range(seq_length):
    count_max = 0
    for i in bases:
        if get_line(i)[m] > count_max:
            count_max = get_line(i)[m]
            consensus[m] = i
with open(args.output, 'w') as o:
    o.write(''.join(consensus) + '\n')


for i in bases:
    list_N = get_line(i)
    with open(args.output, 'a') as o:
        o.write("{}: {}\n".format(i, ' '.join([str(i) for i in list_N])))
```

### 他山之石

```python
strands = [x.strip() for x in f.readlines()]

matrix = zip(*strands)

profile_matrix = map(lambda x: dict((base, x.count(base)) for base in "ACGT"), matrix)

consensus = [max(x,key = x.get) for x in profile_matrix]

print "".join(consensus);

for base in "ACGT":
    print base+":",
    for x in profile_matrix:
        print x[base],
    print ""
```

## 总结

需要学习的函数：

```python
#正则库中的finditer()
#from sys import argv 可简便接受输入项
#for循环接受列表
map()
zip()
f.write('output.file','a') #追加输出
```



## 参考来源

<hr />