---
title: "【debug】Unable to Load Shared Object Stringi"
date: 2021-01-20T15:45:30+08:00
tags: [R, stringi, Manjaro, debug]
categories: []
draft: false
---

在`Manjaro`桌面环境在安装`reshape2`或加载`tidyverse`之中。

<!-- more -->

在安装`reshape2`时出现如下报错

```R
Error in dyn.load(file, DLLpath = DLLpath, ...) : 
  unable to load shared object '/home/chance/R/x86_64-pc-linux-gnu-library/4.0/stringi/libs/stringi.so':
  libicui18n.so.67: cannot open shared object file: No such file or directory
```

第一方案是使用`locate`查找`libicui18n.so.67`这个库。

```bash
locate libicui18n.so.67                             
## /usr/lib/libicui18n.so.67
## /usr/lib/libicui18n.so.67.1
## /usr/lib32/libicui18n.so.67
## /usr/lib32/libicui18n.so.67.1
```

结果发现实际上`libicui18n.so.67`是在当前环境中是存在的，但`stringi`却索引不到。

尝试自定义环境变量`LD_LIBRARY_PATH`在其中包括`/usr/lib/:/usr/lib32/`仍然不能解决问题。

> 实际上，`/usr/lib/`与`/usr/lib32/`均为系统默认的库索引路径。

## 解决方案

这是由于对于`Manjaro`这样的滚动发行版，`stringi`依赖的库是会持续更新变化的。因此，我们需要基于更新的系统库重新编译`stringi`。

```bash
rm -rf /home/chance/R/x86_64-pc-linux-gnu-library/4.0/stringi/
```

在R中以普通用户重新安装`stringi`即可。

```R
install.packages("stringi")
```

## 参考来源

https://archived.forum.manjaro.org/t/manjaro-update-now-r-cant-load-libraries/115419/4

