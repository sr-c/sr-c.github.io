---
title: 【debug】local variable referenced before assignment
tags: [debug, Python]
date: 2018-10-28 22:55:36
permalink:
categories:
description:
---
<p class="description">在函数中调用全局变量时出错</p>

<!-- more -->

## 问题描述

在函数中调用了外部的变量，并改变变量的值，却不声明这是全局变量，会导致Python报错。因为一旦要在函数中改变这个变量的值，就会使得这个变量成为局部变量。

```python
count = 0

def function():    
    count = count + 1
    print(count)
```

> **UnboundLocalError: local variable 'count' referenced before assignment.**
>
> 本地变量'count'在之前已有定义

## 解决方案

在函数中以globlal声明调用的这个变量是全局变量

```python
count = 0
 
def function():
    global count   #declare 'count' used in this function is the global one 
    count = count + 1
    print(count)
```

## Best solution

Don't use `global` variable in a function.

## 参考来源

https://blog.csdn.net/yuli_dai/article/details/9326773

https://stackoverflow.com/questions/11904981/local-variable-referenced-before-assignment

http://blog.sina.com.cn/s/blog_4b9eab320100q2l5.html

<hr />