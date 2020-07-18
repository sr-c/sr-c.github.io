---
title: 解决zlib.h:no such file or directory
tags: [debug]
date: 2018-07-02 23:11:48
permalink:
categories:
description:
---
<p class="description">软件编译过程中zlib出错</p>

<!-- more -->

## 编译安装时报错

`zlib.h:no such file or directory`

网络搜索出的解决方案大多是安装`zlib`

```bash
 yum install zlib-devel
```

但实际上`zlib`已经安装

```bash
rpm search zlib | grep zlib
```

> zlib library files are placed into /usr/local/lib and zlib header files are placed into /usr/local/include, by default.

## 解决方案

实际是本用户的conda环境影响了编译。`zlib`这样系统底层的库，系统并没有缺失。在`~/.bashrc`中屏蔽了conda的字段后，注销重新登陆，重新编译就pass了。

## 参考来源

https://blog.csdn.net/u012724150/article/details/54836441

<hr />