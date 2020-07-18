---
title: 文本处理小技巧
tags: [linux]
date: 2018-11-02 11:54:56
permalink:
categories:
description:
---
<p class="description">linux文本处理小技巧</p>

<!-- more -->

## 删除文件夹软链接

通过`ln -s`方法建立的文件夹软链接，只会将该文件夹链接过来，该文件夹下的文件仍然为原文件，直接删除就是删除原文件。

```bash
#删除软链接的文件夹
rm -rf symbolic_name 
#注意不能有/，否则会删除该软链接文件夹下的所有文件
rm -rf symbolic_name/ 
```

## grep使用多个查询条件

```bash
#使用-E参数兼容正则表达式，注意一定要加引号
grep -E "ESTABLISHED|WAIT"
#或者并列使用多个-e参数
grep -e EST -e WAIT
```

## echo输出换行

```bash
echo -e "Hello, Welcome\ntimes"
```

## sort按列去重

```bash
sort -t ','  -k 1,1 -u 
#-t指定分隔符，-k指定依据第几列到第几列去重
```



## 参考来源

https://www.cnblogs.com/xiaochaohuashengmi/archive/2011/10/05/2199534.html

http://blog.sina.com.cn/s/blog_5ceb51480102wli8.html

http://www.blogjava.net/zhyiwww/archive/2009/01/21/252170.html

https://blog.csdn.net/wqfhenanxc/article/details/81937584

https://segmentfault.com/q/1010000000665713

<hr />