---
title: 读取文件UnicodeDecodeError编码错误
tags: [Python, debug]
date: 2018-11-02 12:45:05
permalink:
categories:
description:
---
<p class="description">Python读取文件UnicodeDecodeError编码错误</p>

<!-- more -->

## 问题描述

```bash
UnicodeDecodeError: 'utf-8' codec can't decode byte 0x96 in position 4575: invalid start byte
```

## 解决方案

```python
import codecs
with open('pepunit.idlist','r',encoding='unicode-escape') as f:
#或忽略错误
with open('pepunit.idlist','r', encoding='utf-8', errors='ignore') as f:
```



## 参考来源

https://www.crifan.com/summary_python_unicodedecode_error_possible_reasons_and_solutions/

<hr />