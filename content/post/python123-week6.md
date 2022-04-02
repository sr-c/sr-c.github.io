---
title: "Python123 Week6"
date: 2022-03-31T18:43:02+08:00
tags: []
categories: []
draft: true
---

组合数据类型，让程序更好地处理一组数据
<!-- more -->
>方法论
python三种主流组合数据类型的使用方法
实践能力
学会编写处理一组数据的能力

## 集合数据类型
### 集合定义
集合是多个元素的无序组合
元素之间无序，每个元素唯一，不可重复
python中要求集合中的元素是不可变的数据类型
集合用大括号表示，元素间用逗号分隔；建立集合类型用{}或`set()`, 建立空集合必须使用`set()`
```python
>>> A = {"python", 123, ("python", 123)}
{"python", 123, ("python", 123)}
>>> B = set("pypy123") #使用set()建立集合
{'1', 'p', '2', '3', 'y'}
```
### 集合操作符
>S|T（并）  
>S-T（差） 
>S&T（交） 
>S^T（补），返回一个新集合，包括集合S和T中的非相同元素
> S <= T 或S < T，返回True/False，判断S和T的子集关系
> S >= T 或S > T，返回True/False，判断S和T的包含关系
 增强操作符
 S|=T
 S-=T
 S&=T
 S^=T，更新集合S,包括集合S和T中的非相同元素
### 处理方法
```python
S.add(x) #如果x不在集合S中，将x增加到S
S.discard(x) #从S中移除元素x，若x不在S中，不报错
S.remove(x) #从S中移除元素x，若x不在S中，则产生KeyError异常
S.clear(x) #移除S中所有元素
S.pop() #随机返回S的一个元素（取出），更新S,若S为空，产生KeyError异常
S.copy()
len(S)
x in S
x not in S
set(x)
```

```python
try:
    while True:
        print(A.pop(), end="")
except:
    pass
```
### 应用场景
包含关系的比较
数据去重
```python
ls = ["p", "p", "s", "s", 123]
s =  set(ls)
ls = list(s)
```
## 序列数据类型
包括元祖类型和列表类型
### 定义
序列是具有先后关系的一组元素
序列是一维元素向量，元素类型可以不同
元素间由序号引导，通过下标访问序列的特定元素
序列是一个基类类型，包括其衍生，如：字符串类型、元祖类型、列表类型。
存在正向递增与反向递减两种方式。
### 处理函数及方法
6个操作符
```python
x in s
x not in s
s + t #连接序列s与t
s*n 或n*s
s[i] #索引
s[i:j]或s[i:j:k] #切片

len(s)
min(s) #s中元素需要可比较
max(s) #s中元素需要可比较，字符之间的比较是按照字母序来比较
s.index(x) 或 s.index(x, i, j) #返回序列s中从i开始到j位置中第一次出现元素x的位置
x.count(x) #序列s中元素x出现的总次数
```
### 元祖类型及操作
元祖是序列类型的一种扩展。元祖一旦创建就不能被修改。
使用小括号()或`tuple()`创建，元素间用逗号`,`分隔
可以使用或不使用小括号
元祖因为创建后不能修改，因此没有特殊操作。

### 列表类型及操作
列表是一种序列类型，创建后可以随意被修改。
>如果使用[]或list()函数，那么就真正的创建了一个列表。
>如果没有使用[]或list()函数创建一个列表，而只是通过赋值方法创建了一个新的列表。事实上只是将同一个列表赋予了一个新的名字，相当于重新命名。
```python
ls[i] = x
ls[i:j:k] lt
del ls[i]
del ls[i:j:k]
ls += lt #更新列表ls，将列表lt元素增加到列表ls中
ls *= n #更新列表ls，其元素重复n次

ls.append() #在列表ls最后增加一个元素x
ls.clear() #删除列表ls中所有元素
ls.copy() #生成一个新列表，赋值ls中所有元素
ls.insert(i, x) #在列表ls的第i位置增加元素x
ls.pop(i) #将列表ls中第i位置元素取出并删除该元素
ls.remove(x) #将列表ls中第一个出现的元素x删除
ls.reverse() #将列表ls中的元素反转
```

### 典型应用场景
元祖用于元素不改变的应用场景，更多用于固定搭配场景
列表更加灵活，它是最常用的序列类型
表示一组有序数据，进而操作它们
#### 元素遍历
```python
for item in ls:
    <语句块>
for item in tp:
    <语句块>
```
#### 数据保护
如果不希望数据被程序所改变，转换成元祖类型（多人程序开发）
```python
ls = tuple(lt)
```
## 实例9: 基本统计值计算
需求：给出一组数，对它们有个概要了解。该怎么做呢？
总个数、求和、平均值、方差、中位数
```python
def getNumber(*d):
    lsNum = []
    for i in d:
        lsNum.append(eval(input))

def getNum(): #获取用户不定长度的输入
    nums = []
    iNumStr = input("请出入数字（回车退出）：")
    while iNumStr != "":
        nums.append(eval(iNumStr))
        iNumStr = input("请出入数字（回车退出）：")
    return nums
def mean(numbers):
    s = 0.0
    for num in numbers:
        s += num
    return s/len(numbers)
def dev(numbers, mean):
    sdev = 0.0
    for num in numbers:
        sdev += pow(num - mean, 2)
    return pow(sdev / (len(numbers) - 1) , 0.5)
def median(numbers):
    numbers = sorted(numbers)
    size = len(numbers)
    if size % 2 == 0:
        return (numbers[(size-1)//2] + numbers[(size+1) // 2]) /2
    else:
        return numbers[size//2]

n = getNum()
m = mean(n)
print("平均值：{}，方差：{:.2f}，中位数：{}".format(m, dev(n, m), median(n)))
```

技术能力扩展
获取多个数据：从控制台获取多个不确定数据的方法
分隔多个函数：模块化设计方法
充分利用函数：充分利用Python提供的内容函数

## 字典数据类型

### 定义

理解映射
映射是一种键（索引）和值（数据）的对应关系。
序列类型由0...N整数作为数据的默认索引；映射类型则由用户为数据定义索引。
字典类型是“映射”的体现
键值对：键是数据索引的扩展
字典是键值对的集合，键值对之间无序
采用`{}`和`dict()`创建，键值对用冒号`:`表示

```python
{<键1>:<值1>, <键2>:<值2>, ...}

>>> de = {} ; type(de)
<class 'dict'>
```
### 处理函数及方法

```python
del d[k] #删除字典d中键k对应的数据值
k in d #判断键k是否在字典d中
d.keys() #返回d中所有键的信息
>>> dict_keys(['中国', '美国', '法国']) #这并不是列表类型，而是字典的键类型
d.values()
d.items()

d.get(k, <default>) #键k存在，则返回相应值，不存在则返回<default>值
d.pop(k, <default>) #键k存在，则取出相应值，不存在则返回<default>值
d.popitem() #随机从字典d中取出一个键值对，以元祖形式返回
d.clear() #删除d中所有的元素
len(d) 

d = {}
d["a"] = 1; d["b"] = 2
d["b"] = 3
"c" in d
len(d)
d.clear()
```

### 应用场景

映射的表达
映射无处不在，键值对无处不在
最主要作用：表达键值对数据，进而操作它们
```python
#由键索引遍历字典d
for k in d:
    <>

```

## jieba模块使用

jieba是优秀的中文分词第三方库
中文文本需要通过分词获得单个的词语
jieba提供三种分词模式

## 参考来源

