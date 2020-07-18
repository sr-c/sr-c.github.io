---
title: blast_error_ncbireg
date: 2018-01-04 22:11:49
tags: [debug]
---

BLAST+ 2.7.1 在运行的时候出现如下类似问题：

``` bash
$ Critical: (110.6) CNcbiRegistry: Syntax error in system-wide configuration file: NCBI C++ Exception: "..........\src\corelib\ncbireg.cpp", line 660: Error: ncbi::IRWRegistry ::x_Read() - Badly placed '\' in the registry value: 'ROOT=J:\nASNLOAD=J:\BioEdi t\tables\nDATA=J:\BioEdit\tables\' (m_Pos = 4)
```
这是由于ncbi.ini文件被bioedit软件损坏所造成的。在C盘windows目录下，找到NCBI.ini，删除。

find ncbi.ini file (C:\windows) and delete it.

再次运行BLAST+，完美解决。

run BLAST+ and the error resolved.

参考来源：http://blog.sina.com.cn/s/blog_14552795a0102whc1.html
