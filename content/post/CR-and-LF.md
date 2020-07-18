---
title: 回车与换行
tags: [vim,Unix]
date: 2018-10-18 15:32:05
permalink:
categories:
description:
---
<p class="description">回车（carriage return）与换行（line feed）</p>

<!-- more -->

## 问题描述

在求一些向量的交集时，发现肉眼可见的交集却得不到结果。

一阵折腾过后，在notepad++中打开，发现在换行时两组数据的差异。CR LF *(\r\n)* 与 LF *(\n)* ，也就是一组数据以\r\n换行，另一组数据以\n换行。

这一差异在vim中`:set list`打开list mode仍然不能区分

## 解决方案

将\r\n替换为\n

> 由于历史遗留问题，在Unix系统中，每行结尾只有"<换行>"，即"\n"；Windows系统里面，每行结尾是"<回车><换行>"，即"\r\n"；Mac系统里，每行结尾是"<回车>"。一个直接后果是，Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；而Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。

## 参考来源

http://www.ruanyifeng.com/blog/2006/04/post_213.html

https://www.cnblogs.com/xiaotiannet/p/3510586.html

<hr />