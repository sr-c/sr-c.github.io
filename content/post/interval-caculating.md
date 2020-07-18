---
title: interval进行区间计算
tags: [python,interval]
date: 2020-07-17 22:23:49
permalink:
categories:
description:
---
<p class="description">使用python的interval进行区间计算</p>

<!-- more -->

# 环境配置

`interval`只有1.0.0一个版本，并不兼容于`python3`. 无法使用`conda`按照，因此直接使用`pip`安装。

```bash
pip install interval
```

# 测试

```python
from interval import Interval, IntervalSet 
# coding
r1 = IntervalSet([Interval(1, 1000), Interval(1100, 1200)])
r2 = IntervalSet([Interval(30, 50), Interval(60, 200), Interval(1150, 1300)])

r3 = IntervalSet([Interval(1000, 3000)])
r4 = IntervalSet([Interval(1000, 3000)])
r5 = IntervalSet([Interval(30000, 12000)])

print (r3 - r4), (r4 - r3), r3 & r4
print len(IntervalSet.empty())

if r3 & r4 == r4:
	print 'yes'

print(r3 & r4)
if (r3 - r4).empty():
   print "true"
print (r3 - r4).empty()

# output
#<Empty> <Empty> [1000..3000]
#0
#yes
#[1000..3000]
#<Empty>

data = [(2,4), (9,13), (6,12)]

Intervals = IntervalSet([Interval.between(min, max) for min, max in data])
print [(_.lower_bound, _.upper_bound) for _ in Intervals]
# output
#[(2, 4), (6, 13)]
```



## 参考来源

https://www.bbsmax.com/A/rV57nE0RJP/

https://www.jianshu.com/p/635069917a83

https://segmentfault.com/q/1010000014045492

<hr />