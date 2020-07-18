---
title: 将Bootstrap值转化为百分数
tags: [Python，debug]
date: 2019-02-22 21:59:21
permalink:
categories:
description:
---
<p class="description">使用工具可视化进化树时，nwk格式的bootstrap值转化为百分数</p>

<!-- more -->

在使用iTOL或者Evolview可视化进化树时，往往nwk格式的bootstrap值需要转化为百分数

## 使用re正则匹配

参考作者使用re正则查找到以`)`开头，`:`结尾，中间是四位小数的数值，去掉头尾之后，转化为浮点数，乘以100，四舍五入，转化为整数，最后转化为字符串。然后使用`re.sun()`进行替换，将原来的小数转化为百分数。


```python
#!/usr/bin/python
# Usage: python convertBootstrap2Integer.py tree.nwk > new.tree.nwk
# The bootstrap data of phylogenetic tree is decimal. This script convert decimal data to percentage without %.

import sys, re
inputNwkTree = sys.argv[1]
treeData = open(inputNwkTree).readlines()[0]
treeData = treeData.strip()
totalDecimalData = re.finditer('\)(\d\.\d{4})\:', treeData)
for item in totalDecimalData:
    matchedData = item.group()
    matchedData = matchedData[1:-1]
    convertedData = '%s' %(int(round(float(matchedData)*100)))
    treeData = re.sub(matchedData, convertedData, treeData)
print treeData
```

## Debug

大部分情况下，该程序工作良好。但在某些时候，得到的进化树存在错误。

这是由于程序直接使用数值进行替换。若在进化树中，该数值还出现在非bootstrap的位置，则同样会被替换，导致生成的树文件出错。要解决此问题，我们可以直接连带bootstrap值前后的`)`与`:`一起作为字符串进行替换，从而避免错误的匹配。

```python
#!/usr/bin/env python
# Usage: python convertBootstrap2Integer.py tree.nwk new.tree.nwk
# The bootstrap data of phylogenetic tree is decimal. This script convert decimal data to percentage without %.
import sys
import re
inputNwkTree = sys.argv[1]
treeData = open(inputNwkTree).readline().strip()
totalDecimalData = re.finditer('\)(\d\.\d{4})\:', treeData)
for item in totalDecimalData:
    matchedData = item.group()
    matchedNum = matchedData[1:-1]
    convertedData = ')%s:' % (int(round(float(matchedNum) * 100)))
    treeData = re.sub(re.escape(matchedData), convertedData, treeData)
with open(sys.argv[2], 'w') as o:
    o.write(treeData)
```

值得注意的是，在第13行`treeData = re.sub(re.escape(matchedData), convertedData, treeData)`中，若直接使用`treeData = re.sub(matchedData, convertedData, treeData)`，则会出现`re.error: unbalanced parenthesis at position 0`报错。这是由于在正则替换时，括号`)`是需要转义的，此时，我们可以使用`re.escape()`进行转义。

## 参考来源

http://blog.sina.com.cn/s/blog_8dac0f5f0102wyz8.html

https://stackoverflow.com/questions/15947140/python-regex-error-unbalanced-parenthesis

<hr />