---
title: 计算基因组中CDS的覆盖度
tags: [cds, python]
date: 2020-06-07 17:53:20
permalink:
categories:
description:
---
<p class="description">计算基因组中CDS的覆盖度</p>

<!-- more -->

已知基因组的注释信息，可以得到任一CDS的位置区间，求所有CDS在基因组中的覆盖度。

## 解决方案

根据`feature table`或`gff/gtf`注释信息可提取到CDS的位置区间。

```bash
grep "CDS" feature_table.ft | \
  awk '{if ($2>$1) printf "%d\t%d\n",$1,$2; else printf "%d\t%d\n",$2,$1}' > cds.interval
```

问题的关键在于多个区间的合并。这其实是[Leetcode56](https://leetcode-cn.com/problems/merge-intervals/solution/he-bing-qu-jian-by-leetcode-solution/) 的题目，因此我们参考使用其答案中的解决方案。

```python
# Definition for an interval.
class Interval:
    def __init__(self, s=0, e=0):
       self.start = s
       self.end = e
# Solution
class Solution:
    def merge(self, intervals):
        """
        :type intervals: List[Interval]
        :rtype: List[Interval]
        """
        if len(intervals) <= 1:
            return intervals
        res = []
        intervals = sorted(intervals,key = lambda start: start.start)
        l = intervals[0].start
        h = intervals[0].end
        for i in range(1,len(intervals)):
            if intervals[i].start <= h:
                h = max(h,intervals[i].end)
            else:
                res.append([l,h])
                l = intervals[i].start
                h = intervals[i].end
        res.append([l,h])
        return res
    
if __name__ == '__main__':

    # Test intervals
    s= Solution()
    i1 = Interval(1,3)
    i2 = Interval(8,10)
    i3 = Interval(2,6)
    i4 = Interval(15,18)
    intervals = [i1,i2,i3,i4]
    res = s.merge(intervals)

    # Read input CDS intervals.
    f = open("cds.intervals")
    cds = f.readlines()
    f.close
    # Merge the intervals.
    cdIn = []
    for i in cds:
        [s, e] = i.strip().split("\t", maxsplit=2)
        cdIn.append(Interval(int(s), int(e)))
    allcds = Solution().merge(cdIn)
    # Count the length of all intervals.
    cdsLength = 0
    for [s, e] in allcds:
        cdslen = e - s + 1
        cdsLength += cdslen
    print(cdsLength)
   
```



## 参考来源

https://blog.csdn.net/qq_34364995/article/details/80788049

<hr />