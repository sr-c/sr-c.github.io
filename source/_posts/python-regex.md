---
title: 【Python】使用正则表达式搜索
tags: [Python, regex]
date: 2018-10-31 22:20:41
permalink:
categories:
description:
---
<p class="description">Python中使用正则表达式搜索</p>

<!-- more -->

## 使用`re`模块

在Python中使用正则表达式时，re模块内部会干两件事情：

1. 编译正则表达式，如果正则表达式的字符串本身不合法，会报错；
2. 用编译后的正则表达式去匹配字符串。

```python
import re
pattern = re.compile(r'[.{1,4}.')
pattern.findall(line)
m = pattern.search(line)
m.group()
```

编译后生成Regular Expression对象，由于该对象自己包含了正则表达式，所以调用对应的方法时不用给出正则字符串。

## 参考来源

http://funhacks.net/2016/12/27/regular_expression/

https://www.jianshu.com/p/2d27ed7fb5cd

https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832260566c26442c671fa489ebc6fe85badda25cd000

<hr />