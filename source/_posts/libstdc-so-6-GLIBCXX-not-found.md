---
title: libstdc++.so.6 GLIBCXX_not_found问题的解决方案
tags: [debug]
date: 2018-06-19 21:26:39
permalink:
categories:
description:
---
<p class="description">由于手动编译了高版本的gcc，却未升级标准库，从而导致的BUG</p>
<!-- more -->
# 确定问题
```bash
cd /usr/lib64/
ls -l | grep libstdc++
#可看到如下问题，libstdc++.so.6链接到了较低版本的库中
lrwxrwxrwx   1 root root       19 Mar  2 19:07 libstdc++.so.6 -> libstdc++.so.6.0.8
-rwxr-xr-x   1 root root  9254976 Mar  2 19:05 libstdc++.so.6.0.21
-rwxr-xr-x   1 root root   936908 Jul 10  2007 libstdc++.so.6.0.8
```
# 解决方案一
在编译gcc的目录中，查找libstdc++相关的库，复制到`/usr/lib64/`目录中，并重新建立`libstdc++.so.6`的软链接。
```bash
#查找libstdc库，高版本gcc编译在/data/software/gcc-7.3.0/中
find /data/software/gcc-7.3.0/ -name libstdc++.so*
```
查找到libstdc库后，切换到`root`用户操作
```bash
#将高版本的动态库复制到/usr/lib64目录
cp /data/software/gcc-7.3.0/x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.24 /usr/lib64
cd /usr/lib64
#删除原来的软链接
rm libstdc++.so.6
#重新链接到高版本的动态库
ln -s libstdc++.so.6.0.24 libstdc++.so.6
```
# 解决方案二
在编译时使用静态链接标准库的方法， 编译时添加  -static-libstdc++选项 。（未尝试）
# 参考链接
https://blog.csdn.net/haoyuedangkong_fei/article/details/50787016
<hr />