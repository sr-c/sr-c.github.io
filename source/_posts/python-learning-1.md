---
title: Python语言基础
tags: [Python]
date: 2018-10-25 22:33:08
permalink:
categories:
description:
---
<p class="description">python语言基础</p>

<!-- more -->

## 数据类型

### 变量

变量的动态类型：整型（int），浮点型（float），字符型（str）

#### 基本运算

\+，\-，\*，/，次方（**），整除（//），取余（%）

四舍五入 round()

取整 int()

#### 高级功能

```python
import math

trunc()
ceil()
floot()
fabs()
sqrt()

import random

uniform(a,b)
randint(a,b)
```

#### 字符型变量

**转义字符**

`\t` `\n` `\"` `\'`

忽略多个转义字符，可在字符串前加`r`

**常用操作**

连接 `+`

重复 `s*5`

取值/切片

> 字符型变量可被视作数组处理

#### 函数/方法

```python
len(s)
s.upper()
s.lower()
s.strip()
s.replace()
s.index()
s.split()
s.count()
```

### 数组/序列

列表（list） 定义：chr_len = [10, 20, 30]

元祖（tuple） 定义：chr_len = (10, 20, 30)

> 元祖不支持原位改变

#### 列表特有操作

```python
#增加元素
append()
extend()
insert()
#删除元素
del
remove()
pop()
#原位改变
reverse()
sort()
#拷贝列表
copy()
```

**列表生成式**

```python
['Chr'+str(i) for i in chr if isinstance(i,int)]
#操作 + 循环 + 条件
```

### 哈希/字典

**定义**： chr = {'chr1':10, 'chr2':20, 'chr3':30}

**特征**： 无序，键唯一

**调用** 

```python
chr['chr1']
chr.get('chr1','')
```

**键，值和项目**

```python
chr.keys()
chr.values()
chr.items()
```

#### 常用操作

```python
#添加项目
chr.update()
#删除项目
del chr['chr1']
chr.pop()
chr.popitem()
chr.clear()
#求长度
len(chr)
```

## 输入输出

```python
#打开句柄
f = open(f_fasta,'r')
f.read() #读取所有内容，返回字符串
f.readlines() #读取所有内容，按行分割，返回列表
f.readline() #读取一行内容，返回字符串
#关闭文件
f.close()

#写入一个字符串
f.write()
#写入一个序列型变量
f.writelines()
```

更简洁写法

```python
with open(f_fasta,'r') as lines:
	for line in lines:
        ...

with open(args.out,'w') as f:
    f.write()
    ...
```

## argparse 更专业的参数输入

```python
import argparse

parser = argparse.ArgumentParaser()
parser.add_argument('...',
                   type=str,
                   nargs='+',
                   #action='store_true',
                   #default='',
                   required=True,
                   help='')
args = parser.parse_args()
```

## 格式化字符串

```python
print("...{2:^16s}...{0:0>6.2f}...{}...".format("total length:","total_len"))
```

格式化输出指定输出格式的方式：

> 元素下标/名：填充符，对齐方式，字符宽度，[浮点精度]，变量类型

## 参考来源

http://www.genek.tv/my/course/194

<hr />