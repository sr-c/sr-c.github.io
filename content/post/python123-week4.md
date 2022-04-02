---
title: "Python123 Week4"
date: 2022-03-29T08:09:30+08:00
tags: [Python]
categories: []
draft: true
---

学习python国家精品课程，第四周 程序的结构控制

<!-- more -->
## 程序的分支结构
### 单分支结构
最简单的`if` 结构
### 二分支结构
```python
if <条件>：
    <语句块>
else:
    <语句块>
```
此外还有简略的紧凑形式：`<表达式1> if <条件> else <表达式2>`
>表达式不同于语句，不支持带符号的赋值形式。
### 多分支结构
```python
if <>:
    <>
elif <>:
    <>
……
else:
    <>
```
#### 异常处理
```python
try:
    <语句块1>
except <异常类型>:
    <语句块2>
else:
    <语句块3>
finally:
    <语句块4>
```
>其中，`finally`语句一定执行，`else`语句在不发生异常时执行。

## 程序的循环结构
### 遍历循环`for`
计数循环
### 无限循环`while`
```python
while <条件>:
    <语句块>
```
反复执行语句块，直至条件不被满足。
```python
def dayUP(df):
    dayup = 1
    for i in range(365):
        if i % 7 in [0, 6]:
            dayup *= 1 - 0.01
        else:
            dayup *= 1 + df
    return dayup
dayfactor = 0.01
while dayUP(dayfactor) < 37.78:
    dayfactor += 0.001
print("工作日的努力参数是：{:.3f}".format(dayfactor))
```
### 控制保留字
`break` 跳出并结束当前整个循环
`continue` 结束当次循环，继续后续次数循环
>`break`仅跳出当前循环层次
### 循环与`else`
循环在未被`break`时，执行`else`语句块。`else`作为“正常”完成循环的奖励。
```python
logCount = 0
while logCount < 3:
    userName = input()
    passWord = input()
    if userName == "Kate" and passWord == "666666":
        print("登录成功！")
        break
    else:
        logCount += 1
```

#### 圆周率计算
蒙特利尔方法
```python

```
## 参考来源

