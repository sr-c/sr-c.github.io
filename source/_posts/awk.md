---
title: 使用awk提取文件内容
tags: [awk]
date: 2018-06-30 13:11:16
permalink:
categories:
description:
---
<p class="description">AWK 是一种使用方便且表现力很强的编程语言，方便进行文本的处理</p>

<!-- more -->

## 语法

```bash
awk 'program' input files
awk -f progfile optional list of files
```

简单的单行awk命令格式如下，其命令由一个单独的 模式–动作 语句 (pattern-action statement) 组成

```bash
awk '[pattern] {action}' {filenames}   # 行匹配语句 awk '' 只能用单引号
```

## AWK程序的结构

每一个`awk`程序都是由一个或多个`模式–动作` 语句组成的序列:

```
pattern { action }
pattern { action }
...
```

`awk`的基本操作是在由输入行组成的序列中, 陆续地扫描每一行, 搜索可以被模式 匹配 (match) 的行。
“匹配” 的精确含义依赖于问题中的模式, 比如, 对于`$3 > 0`, 意味着 “条件为真”. 

每一个输入行轮流被每一个模式测试。 每匹配一个模式, 对应的动作 (可能包含多个步骤) 就会执行。然后下一行被读取, 匹配重新开始。这个过程会一起持续到所有的输入被读取完毕为止。
上面的程序是模式与动作的典型例子. 程序

> $3 == 0 { print $1 }

由一条单独的`模式–动作`语句组成: 如果某行的第 3 个字段为 0, 那么它的第 1 个字段就会被打印出来.

## 内建变量

`awk`从它的输入中每次读取一行，将行分解为一个个的字段 (默认将字段看作是非空白字符组成的序列)。当前输入行的第一个字段叫作`$1`, 第二个是`$2`, 依次类推，一整行记为`$0`. 每行的字段数有可能不一样。

`NF`, 当前输入行的字段数量

`NR`, 到目前为止，读取到的行的数量

## 标准化输出

`printf`语句具有形式

> printf(*format, value 1 , value 2 , ... , value n* )
>
> { printf("total pay for %s is $%.2f\n", $1, $2 * $3) }

`format`是一个字符串, 它包含按字面打印的文本, 中间散布着格式说明符, 格式说明符用于说明如何打印值. 一个格式说明符是一个 %, 后面跟着几个字符, 这些字符控制一个`value`的输出格式. 第一个格式说明符说明 `value 1`的输出格式, 第二个格式说明符说明 `value 2`的输出格式, 依次类推。 于是, 格式说明符的数量应该和被打印的 value 一样多。

### 格式说明符

`%s`, 以字符串的形式打印

`%.2f` , 按照数值格式打印，且带有两位小数

`%-8s` , 将_value_以左对齐字符形式输出，占用8个字符的宽度

`%6.2f` , 将_value_以带有两位小数的数值形式输出，至少占用6个字符的宽度

## 参考来源

The AWK Programming Language (Aho, Kernighan, Weinberger 著, 中文名: AWK 程序设计语言) 

https://github.com/wuzhouhui/awk

<hr />