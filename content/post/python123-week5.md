---
title: "Python123 Week5"
date: 2022-03-29T10:25:51+08:00
tags: [Python]
categories: []
draft: true
---
第5周 函数和代码复用
<!-- more -->
>**方法论**
Python基本代码抽象即函数的使用方法
**实践能力**
学会编写带有函数并复用代码的程序
## 函数的定义与使用
### 定义
函数是一段代码的表示，
是一段具有特定功能的、可重用的语句组
是一种功能的冲向，一般函数表达特定功能
两个作用：**降低编程难度** 和 **代码复用**
```python
def <函数名>(<参数(0个或多个)>):
    <函数体>
    return <返回值>
```
函数在定位时，所指定的参数是一种占位符
函数定义后， 如果不经过**调用**，不会被运行
函数定义时，参数是输入、函数体是处理、结果是输出（IPO）
函数就是IPO的一种实现

### 调用
调用是运行函数代码的方式
调用势要给出实际参数；实际参数替换定义中的参数；函数调用后得到返回值。

### 函数的参数传递
函数可以由参数，也可以没有，但必须保留括号。
#### 可选参数传递
在函数定义时，可选参数必须放在非可选参数之后。
```python
#计算n!//m
def fact(n, m=1):
    s = 1
    for i in range(1, n+1):
        s *= i
    return s//m
```
#### 可变参数传递
函数定义时可以设计可变数量参数，即不确定参数总数量。
```python
#计算n!乘数
def fact(n, *b):
    s = 1
    for i in range(1, n+1):
        s *= i
    for item in b:
        s *= item
    return s
```
函数调用时，可以使用位置或名称方式传递。
#### 函数的返回值
函数可以返回0个或多个结果
`return`保留字用来传递返回值
函数可以有返回值，也可以没有，可以有`return`，也可以没有。
return可以返回0个或多个结果
```python
def fact():

    return s//m, m, n
```
返回的是使用小括号包含的元祖类型
#### 局部变量和全局变量
>局部变量：只在函数体中使用的变量
>全局变量：整个函数使用的变量

规则1: 局部变量和全部变量是不同变量
-局部变量是函数内部的占位符，与全局变量可能重名，但不同
-函数运算结束后，局部变量被释放
-可以使用`global`保留字在函数内部使用全局变量
```python
n, s = 10, 100
def fact(n):
    global s
    for i in range(1, n+1):
        s *= i
    return s
print(fact(n), s)
```
规则2: 若局部变量为**组合数据类型**，且未创建，则等同于全局变量
```python
ls = ["F", "f"]
def func(a):
    ls.append(a)
    return ls
func("C")
print(ls)
------
输出为['F', 'f', 'C']
```

```python
ls = ["F", "f"]
def func(a):
    ls = []
    ls.append(a)
    return ls
func("C")
print(ls)
------
输出为['F', 'f']
```

>组合数据类型在python中是由指针来体现的。如果在函数中使用了全局变量，就修改了指针对应的内容。函数中如果没有真实创建组合数据类型，它使用的变量是使用的指针，而指针指的是外部的全局变量。所以修改这个指针对应的内容，就修改了全局变量。

#### lambda 函数
lambda函数是一种匿名函数，即没有名字的函数
使用`lambda`保留字定义，函数名是返回结果
lambda函数用于定义简单的、能够在一行内表示的函数
`<函数名> = lambda <参数>: <表达式>`
```python
>>> f = lambda x, y : x + y
>>> f(10, 25)
>>> f = lambada: "lambda函数"
>>> print(f())
```
谨慎使用lambda函数
lambda函数主要用作一些特定函数或方法的参数
有一些固定使用方式，建议逐步掌握
还是建议使用`def`方式定义函数

## 实例7:七段数码管绘制
使用turtle函数绘制turtle数码管
1. 绘制单个数字对应的数码管
2. 获得一串数字，绘制对应的数码管
3. 获得当前系统时间，绘制对应的数码管
```python
#SevenDigitsDrawV1.py
import turtle
def drawLine(draw):  #绘制单段数码管
    turtle.pendown() if draw else turtle.penup()
    turtle.fd(40)
    turtle.right(90)
def drawDigit(digit):  #根据数字绘制七段数码管
    drawLine(True) if digit in [2, 3, 4, 5, 6, 8, 9] else drawLine(False)
    drawLine(True) if digit in [0, 1, 3, 4, 5, 6, 7, 8, 9] else drawLine(False)
    drawLine(True) if digit in [0, 2, 3, 5, 6, 8, 9] else drawLine(False)
    drawLine(True) if digit in [0, 2, 6, 8] else drawLine(False)
    turtle.left(90)
    drawLine(True) if digit in [0, 4, 5, 6, 8, 9] else drawLine(False)
    drawLine(True) if digit in [0, 2, 3, 5, 6, 7, 8, 9] else drawLine(False)
    drawLine(True) if digit in [0, 1, 2, 3, 4, 7, 8, 9] else drawLine(False)
    turtle.left(180)
    turtle.penup() #为后续数字绘制确定位置
    turtle.fd(20)  #为会须数字绘制确定位置
def drawDate(date):  #获得要输出的数字
    for i in date:
        drawDigit(eval(i)) #通过eval()函数将数字变为整数
def main():
    turtle.setup(800, 350, 200, 200)
    turtle.penup()
    turtle.fd(-300)
    turtle.pensize(5)
    drawDate('20181010`)
    turtle.hideturtle()
    turtle.done()
main()
```
优化绘制，给线条之间留出一些间隔
```python
def drawGap():
    turtle.penup()
    turtle.fd(5)
def drawLine(draw):
    drawGap()
    turtle.pendown() if draw else turtle.penup()
    turtle.fd(40)
    drawGap()
    turtle.right(90)
......

```
使用`time`库中的`gmtime()`函数获取系统时间，`strftime()`函数格式化输出时间。
```python
import turtle, time
......(略)
def drawDate(date): #date为日期，格式为`%Y-%m=%d+`
    turtle.pencolor("red")
    for i in date:
        if i == "-":
            turtle.write("年", font = "Arial", 18, "normal")
            turtle.pencolor("green")
            turtle.fd(40)
        elif i == "=":
            turtle.write("月", font = "Arial", 18, "normal")
            turtle.pencolor("blue")
            turtle.fd(40)
        elif i == "+":
            turtle.write("日", font = "Arial", 18, "normal")
        else:
            drawDigit(eval(i))
def main():
    turtle.setup(800, 350, 200, 200)
    turtle.penup()
    turtle.fd(-300)
    turtle.pensize(5)
    drawDate(time.strftime('%Y-%m=%d+', time.gmtime()))
    turtle.hideturtle()
    turtle.done()

```

>模块化思维：确定模块接口，封装功能
>规则化思维：抽象过冲为规则，计算机自动执行
>化繁为简：将大功能变为小功能组合，分而治之

##### 思考题
1. 绘制带小数点的七段数码管
2. 带刷新的时间倒计时效果
3. 绘制高级的数码管（中间有“米”型数码管）

## 代码复用与函数递归
### 代码复用
把代码当成资源进行抽象
代码资源化：程序代码是一种用来表达计算的“资源”
代码抽象化：使用函数等方法对代码赋予更高级别的定义
代码复用：同一份代码在需要时可以被重复使用

通过函数和对象实现代码复用的两种主要形式
函数：将代码命名，在代码层面简历了初步抽象
对象：通过属性和方法，`<a>.<b>`或`<a>.<b>()`, 在函数之上再次组织进行抽象。

分而治之
通过函数或对象封装将程序划分为模块及模块间的表达。
具体包括：主程序、子程序和子程序间关系
分而治之、分层抽象、体系化的设计思想

**紧耦合与松耦合**
两个部分之间交流很多，无法独立存在
两个部分之间交流较少，可以独立存在

模块内部紧耦合、模块之间松耦合。

### 函数递归
函数定义中调用函数自身的方式。
链条：计算过程存在递归链条
基例：存在一个或多个不需要再次递归的基例

#### 数学归纳法:
1. 证明当n取第一个值n<sub>0</sub>时命题成立
2. 假设当n~k~时命题成立，证明当n=n~(k+1)~时命题也成立

```python
def fact(n):
    if n == 0:
        return 1
    else:
        return n * fact(n-1)
```
实现递归需要函数结合分支语句来实现。
递归本身是一个函数，需要函数定义方式描述。
函数内部，采用分支语句对输入参数进行判断。
基例和链条，分别编写对应代码。


```python
# 函数 + 分支结构
# 递归链条
# 递归基例

##### 字符串反转
>>> s[::-1]
def rev(s):
    if s == "":
        return ""
    else:
        # return s[-1] + rev(s[:-1])
        return rev(s[1:]) + s[0]

## 斐波那契数列
def feb(n):
    if n in [1, 2]:
        return 1
    else:
        return feb(n-1) + feb(n-2)

## 汉诺塔问题
count = 0
def hanoi(n, src, dst, mid):
    global count
    if n == 1:
        print("{}:{}->{}".format(1, src, dst))
        count += 1
    else:
        hanoi(n-1, src, mid, dst)
        print("{}:{}->{}".format(n, src, dst))
        count += 1
        hanoi(n-1, mid, dst, src)
        
````
## 模块4:PyInstaller的使用

## 实例8:科赫雪花
一种分形几何图案


## 参考来源

