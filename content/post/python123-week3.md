---
title: "Python123 Week3"
date: 2022-03-29T10:26:32+08:00
tags: [Python]
categories: []
draft: true
---

基本数据类型

<!-- more -->
## 数字类型及操作
### 整数类型
数字类型包括4种：
十进制；二进制，以0b或0B开口；八进制，以0o或0O开头；十六进制，以0x或0X开头。
幂函数`pow(2, 100)`
### 浮点数类型
带有小数点及小数位字符，其**取值范围和精度都存在限制**。
>取值范围约-10^308 至10^308, 精度数量级至10^-16
因此，浮点数间运算存在不确定尾数，这不是bug。
一般使用53位二进制标识小数部分。二进制表示小数，可以无限接近，但不完全相同。
```python
round(0.1+0.2, 1) == 0.3
round(x, d) #对x四舍五入，d是小数截取位数
```
浮点数运算及比较需要round函数辅助，可使用科学计数法计数，`<a>e<b>`
### 复数类型
`z.real()`获得实部；`z.image()`获得虚部。
### 增强操作符
`x = x op y` 其中，op为二元操作符
`x += y`
>整数类型的无限范围及4种进制表示
浮点数类型的近似无限范围、小尾数及科学计数法
+, -, *, /, //, %, **, 二元曾就赋值操作符
abs(), divmod(), pow(), round(), max(), min()
int(), float(), complex()

由`for ... in ...`(计算思维) 转变为`def ... while ...`“笨办法”试错。
## 字符串类型及操作
字符中处理函数
len(), str()
hex()或oct()
chr()或ord()，进行unicode与单字符之间的相互转换。
### 字符串处理方法
“方法”特指`<a>.<b>()` 风格中的函数`<b>()`
方法也是函数，但与`<a>`有关，仅限`<a>.<b>()`风格使用。体现的是面向对象`<a>`编程。
```python
str.lower()或srt.upper()
str.split(sep=None)
str.count(sub)
str.replace(old, new)
str.center(width[, fillchar]) #根据宽度居中
str.strip(chars)
str.join(iter) #在iter变量中增加一个str, 如",".join("12345")
```
#### `.format()`格式化方法
`{}`称作槽，是一种占位信息符。
```python
`{<参数序号>:<格式控制标记>}`
:<填充><对齐><宽度><,><.精度><类型>
"{0:=^20}.format("PYTHON")"
```
其中的前三项代表对齐方式，后三项代表精度方式。

#### 文本进度条
```python
import time
scale = 50
print("执行开始".center(scale//2, "-"))
start = time.perf_counter()
for i in range(scale+1):
    a = "*" * i
    b = "." * (scale - i)
    c = (i/scale) * 100
    dur = time.perf_counter() - start
    print("\r{:^3.0f}%[{}->{}]{:.2f}s".format(c, a, b, dur), end = "")
    time.sleep(0.1)
print("\n" + "{:-^{}}".format("执行结束", scale//2))
```
## 参考来源

