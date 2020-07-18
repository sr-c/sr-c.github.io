---
title: vim使用的小技巧
tags: [vim,vi,unix]
date: 2018-07-06 17:52:33
permalink:
categories:
description:
---
<p class="description">在`vim`/`vi`中，可以使用一些命令进行文本操作，掌握之后能够高效地达成目标。</p>

<!-- more -->

## 一、查找      

**查找命令**          

```bash
/pattern<Enter> ：向下查找pattern匹配字符串     
?pattern<Enter>：向上查找pattern匹配字符串     
```

使用了查找命令之后，使用如下两个键快速查找：     
n：按照同一方向继续查找     
N：按照反方向查找     
**字符串匹配**          
`pattern`是需要匹配的字符串，例如： 

```bash
/abc<Enter>      #查找abc
/ abc <Enter>    #查找abc单词（注意前后的空格） 
```

除此之外，`pattern`还可以使用一些特殊字符，包括（/、^、$、*、.），其中前三个这两个是vi与vim通用的，`/`为转义字符。 

```bash
/^abc<Enter>    #查找以abc开始的行 
/test$<Enter>    #查找以abc结束的行 
//^test<Enter>    #查找^tabc字符串
```

## 二、替换     

**基本替换**      

```bash
:s/vivian/sky/         #替换当前行第一个 vivian 为 sky
:s/vivian/sky/g     #替换当前行所有 vivian 为 sky
:n,$s/vivian/sky/     #替换第 n 行开始到最后一行中每一行的第一个 vivian 为 sky
:n,$s/vivian/sky/g     #替换第 n 行开始到最后一行中每一行所有 vivian 为 sky
（n 为数字，若 n 为 .，表示从当前行开始到最后一行）
:%s/vivian/sky/        #（等同于 :g/vivian/s//sky/） 替换每一行的第一个 vivian 为 sky
:%s/vivian/sky/g    #（等同于 :g/vivian/s//sky/g） 替换每一行中所有 vivian 为 sky
```

 **可以使用 `#`或`+` 作为分隔符，此时中间出现的 `/`不会作为分隔符**      

```bash
:s#vivian/#sky/#         ##替换当前行第一个 vivian/ 为 sky/
:%s+/oradata/apras/+/user01/apras1+  ##（使用+替换/）/oradata/apras/替换成/user01/apras1/
```

**删除文本中的`^M`**      

> 问题描述：对于换行，window下用回车换行（0A0D）来表示，linux下是回车（0A）来表示。这样，将window上的文件拷到unix上用时，总会有个`^M`，请写个用在unix下的过滤windows文件的换行符（0D）的shell或c程序。     

使用命令：cat filename1 | tr -d “^V^M” > newfile;     
使用命令：sed -e “s/^V^M//” filename > outputfilename     
需要注意的是在1、2两种方法中，^V和^M指的是Ctrl+V和Ctrl+M。你必须要手工进行输入，而不是粘贴。     
在`vi`中处理：首先使用`vi`打开文件，然后按ESC键，接着输入命令：

```bash
:%s/^V^M//
:%s/^M$//g
```

如果上述方法无用，则正确的解决办法是：

```
tr -d “/r” < src >dest
tr -d “/015″ dest

strings A>B
```

**其他用法**      

```
:s/str1/str2/          #用字符串 str2 替换行中首次出现的字符串 str1
:s/str1/str2/g         #用字符串 str2 替换行中所有出现的字符串 str1
:.,$ s/str1/str2/g     #用字符串 str2 替换正文当前行到末尾所有出现的字符串 str1
:1,$ s/str1/str2/g     #用字符串 str2 替换正文中所有出现的字符串 str1
:g/str1/s//str2/g      #功能同上
```

从上述替换命令可以看到：    

> g 放在命令末尾，表示对指定行的搜索字符串的每次出现进行替换；不加 g，表示只对指定行的搜索字符串的首次出现进行替换；     
>
> g 放在命令开头，表示对正文中所有包含搜索字符串的行进行替换操作。     

即：命令的开始可以添加影响的行，如果为g表示对所有行；命令的结尾可以使用g来表示是否对每一行的所有字符串都有影响。     

## 三、简单的vim正则表达式规则

在`vim`中有四种表达式规则：     

> magic(/m)：除了$.*^之外其他元字符都要加反斜杠     
>
> nomagic(/M)：除了$^之外其他元字符都要加反斜杠     
>
> /v（即 very magic 之意）：任何元字符都不用加反斜杠     
>
> /V（即 very nomagic 之意）：任何元字符都必须加反斜杠     

`vim`默认使用`magic`设置，这个设置也可以在正则表达式中通过 `/m` `/M` `/v` `/V`开关临时切换。例如： 

```
//m.*          # 查找任意字符串
//M.*          # 查找字符串 .* （点号后面跟个星号）

//v(a.c){3}$   # 查找行尾的abcaccadc
//m(a.c){3}$   # 查找行尾的(abc){3}
//M(a.c){3}$   # 查找行尾的(a.c){3}
//V(a.c){3}$   # 查找任意位置的(a.c){3}$
```

推荐使用默认的`magic`设置，在这种情况下，常用的匹配有：

```
//<abc          #查找以test开始的字符串
/abc/>          #查找以test结束的字符串

$       匹配一行的结束
^       匹配一行的开始
//<abc<Enter>:查找以abc开始的字符串
/>      匹配一个单词的结束，例如/abc/><Enter>:查找以abc结束的字符串

*       匹配0或多次
/+      匹配1或多次
/=      匹配0或1次
.       匹配除换行符以外任意字符
/a      匹配一个字符
/d      匹配任一数字
/u      匹配任一大写字母
[]      匹配范围，如t[abcd]s 匹配tas tbs tcs tds
/{}     重复次数，如a/{3,5} 匹配3~5个a
/( /)   定义重复组，如a/(xy/)b 匹配ab axyb axyxyb axyxyxyb ...
/|      或，如：for/|bar 表示匹配for或者bar

/%20c   匹配第20列
/%20l   匹配第20行
```

关于正则表达式的详细信息，请参见参考文献。

**参考文献：**  

[vi中的正则表达式](http://tech.idv2.com/2008/07/08/vim-regexp/)   [vim运用正则表达式进行查找替换](http://blog.chinaunix.net/u1/34252/showart_1075923.html)   [vi替换字符串](http://blog.csdn.net/aldenphy/archive/2009/03/24/4019486.aspx) 

## 参考来源

https://blog.csdn.net/lanxinju/article/details/5731843

http://man.linuxde.net/vi

https://harttle.land/2016/08/08/vim-search-in-file.html

<hr />