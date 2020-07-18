---
title: Python练习-2
tags: [Python, Rosalind]
date: 2018-10-30 17:58:54
permalink:
categories:
description:
---
<p class="description">在Rosalind上进行练习</p>

<!-- more -->

## 查找核酸序列的子集

http://rosalind.info/problems/subs/

```python
with open('rosalind_subs.txt') as lines:
    string_1 = lines.readline().strip()
    string_2 = lines.readline().strip()

    note = []
    length_2 = len(string_2)
    for i in range(0, len(string_1)+1):
        if string_1[i:i+length_2] == string_2:
            note.append(str(i+1))

    notes = ' '.join(note)
print(notes)
```

## 计算两条序列间的汉明距离

http://rosalind.info/problems/hamm/

```python
with open('rosalind_hamm.txt') as f:
    s,t = f.read().split()

count_n = 0
for i in range(len(t)):
    if not s[i] == t[i]:
        count_n += 1

print(count_n)
```

## 将核酸序列转换为蛋白序列

http://rosalind.info/problems/prot/

```python
#!/usr/bin/env python
def prot(condon):
    if condon in ['GCU', 'GCC', 'GCA', 'GCG']:
        return 'A'
    elif condon in ['CGU', 'CGC', 'CGA', 'CGG', 'AGA', 'AGG']:
        return 'R'
    elif condon in ['AAU', 'AAC']:
        return 'N'
    elif condon in ['GAU', 'GAC']:
        return 'D'
    elif condon in ['UGU', 'UGC']:
        return 'C'
    elif condon in ['CAA', 'CAG']:
        return 'Q'
    elif condon in ['GAA', 'GAG']:
        return 'E'
    elif condon in ['GGU', 'GGC', 'GGA', 'GGG']:
        return 'G'
    elif condon in ['CAU', 'CAC']:
        return 'H'
    elif condon in ['UUA', 'UUG', 'CUU', 'CUC', 'CUA', 'CUG']:
        return 'L'
    elif condon in ['AUU', 'AUC', 'AUA']:
        return 'I'
    elif condon in ['AAA', 'AAG']:
        return 'K'
    elif condon in ['AUG']:
        return 'M'
    elif condon in ['UUU', 'UUC']:
        return 'F'
    elif condon in ['CCU', 'CCC', 'CCA', 'CCG']:
        return 'P'
    elif condon in ['UCU', 'UCC', 'UCA', 'UCG', 'AGU', 'AGC']:
        return 'S'
    elif condon in ['ACU', 'ACC', 'ACA', 'ACG']:
        return 'T'
    elif condon in ['UGG']:
        return 'W'
    elif condon in ['UAU', 'UAC']:
        return 'Y'
    elif condon in ['GUU', 'GUC', 'GUA', 'GUG']:
        return 'V'
    elif condon in ['AUG', 'UAA', 'UGA', 'UAG']:
        return ''
    else:
        print('Not proper condon input.')

with open('rosalind_prot.txt') as f:
    RNA_string = f.readline().strip()
    p_len = int(len(RNA_string)/3)
    prots = [prot(RNA_string[i*3:i*3+3]) for i in range(p_len)]
    prot_string = ''.join(prots)

print(prot_string)
```

### 判断一个能否被整除

```python
if x % 3 == 0:
    print "x能够被3整除"
else:
    print "x不能够被3整除"
```

### 查找列表

```python
'a' in a_list
a_list.index('a')
a_list.find('a')
```

## 参考来源

https://en.wikipedia.org/wiki/Hamming_distance

https://blog.csdn.net/qq_31747765/article/details/80944227

https://blog.csdn.net/qq842977873/article/details/79372844

<hr />