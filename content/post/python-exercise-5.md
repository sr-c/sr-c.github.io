---
title: Python练习5
tags: [Python, Rosalind]
date: 2019-02-19 16:20:13
permalink:
categories:
description:
---
<p class="description">在Rosalind上进行练习</p>

<!-- more -->

## 计算肽链的分子量

http://rosalind.info/problems/prtm/

值得注意的是，最后一个氨基酸中一个水分子的分子量18.01056 Da. 但我的结果中没有加上这个水，仍然提示我为正确答案，可见还是比较宽松的。

```python
#!/usr/bin/env python
mass_table = dict(A=71.03711, C=103.00919, D=115.02694, E=129.04259, F=147.06841, G=57.02146, H=137.05891, I=113.08406,
                  K=128.09496, L=113.08406, M=131.04049, N=114.04293, P=97.05276, Q=128.05858, R=156.10111, S=87.03203,
                  T=101.04768, V=99.06841, W=186.07931, Y=163.06333)
prot_mass = 0
with open("rosalind_prtm.txt") as prot:
    pro_line = prot.readline().strip()
#    for i in range(len(pro_line)):
#        prot_mass += mass_table[pro_line[i]]
    for i in pro_line:
        prot_mass += mass_table[i]
    print(prot_mass)
```



### 他山之石

快速输入字符串，并转换为字典的方式值得学习。

```python
mmt = """
A   71.03711
C   103.00919
D   115.02694
E   129.04259
F   147.06841
G   57.02146
H   137.05891
I   113.08406
K   128.09496
L   113.08406
M   131.04049
N   114.04293
P   97.05276
Q   128.05858
R   156.10111
S   87.03203
T   101.04768
V   99.06841
W   186.07931
Y   163.06333
""".split()
mmt = dict(zip(mmt[::2],map(float,mmt[1::2])))

s = "SKADYEK"
print "%.2f"%(sum(map(lambda x:mmt[x],s))+18.01528)
```

## 孟德尔第二定律

http://rosalind.info/problems/lia/

亲本(第0代)的基因型为`AaBb`，与基因型同为`AaBa`的个体产生2个后代。之后，每个个体产生2个后代，且均与`AaBa`基因型的个体婚配。求在第k代的所有个体中，至少有N个个体的基因型为`AaBb`的概率。

> 题干中专门提示了不需要计算每一代中个体AaBb基因型婚配的具体情况。这是因为每代的个体都与AaBa婚配，因此种群中A/a,B/b基因型的频率保持不变，一直为1/2。因此每一代中个体为AaBb基因型的概率均为1/2*1/2=1/4。
>
> 接下来就是一个高中概率论的排列组合问题。

```python
#!/usr/bin/env python
with open("rosalind_lia.txt") as f:
    (k, N) = f.readline().strip().split(" ")
    k = int(k)
    N = int(N)

#计算排列数A(m, n)
def aR(m, n):
    be = 1
    for i in range(n):
        be *= m - i
    return be

#计算组合数C(m, n)
def cR(m, n):
    if m < n:
        print("error input")
    else:
        return aR(m, n) / aR(n, n)

#计算第k代(2^k个体)中至少含有N个0.25概率事件发生的总概率
rate = 0
for i in range(N):
    rate += cR(2 ** k, i) * 0.25 ** i * 0.75 ** (2 ** k - i)

ans = 1 - rate
print(ans)

```



### 他山之石

其他人的答案中，使用R能够更方便地进行这个排列组合计算

```R
pbinom(N-1, 2^k, 0.25, lower.tail=F)
```

python中使用scipy也可以计算排列组合

```python
>> from scipy.special import comb, perm
>> perm(3, 2)
6.0
>> comb(3, 2)
3.0
```



## 参考来源

http://rosalind.info/problems/prtm/solutions/

http://rosalind.info/problems/lia/solutions/

https://blog.csdn.net/lanchunhui/article/details/51824602

<hr />