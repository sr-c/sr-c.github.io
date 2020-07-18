---
title: LaTeX-beginer
tags: [LaTex]
date: 2020-06-19 21:49:12
permalink:
categories:
description:
---
<p class="description"></p>

<!-- more -->

# 环境配置

安装`TexLive`与`vscode`, 具体流程参考[其他说明](https://blog.csdn.net/ddydavie/article/details/83020615)。

# 文献管理格式BibTeX

使用BibTex管理参考文献。

## 规范格式

```tex
@article{mrx05, 
auTHor = "Mr. X", 
Title = {Something Great}, 
publisher = "nob" # "ody", 
YEAR = 2005, 
} 
```

## CNKI导出BibTeX

CNKI官方系统并不支持直接导出`BibTeX`，如果不能使用`Google schloar`，那就只能使用曲线方案。

1. 使用Vopaaz开发的转化[方案](https://github.com/Vopaaz/CNKI_2_BibTeX)，从CNKI导出为`NoteExpress`格式，再转化为`BibTeX`。
2. 将文献导入Zotero/Endnote, 再导出为`BibTeX`

## 注意事项

德语中的一些曲音字母（如 `ä`,`ö`,`ü` ）在直接编译时存在问题。这时我们可写成`{\"a}`, `{\"o}`, `{\"u}`来[解决问题](https://tex.stackexchange.com/a/257781)。

## 参考来源

https://blog.csdn.net/ddydavie/article/details/83020615

https://stackoverflow.com/a/58667452

<hr />