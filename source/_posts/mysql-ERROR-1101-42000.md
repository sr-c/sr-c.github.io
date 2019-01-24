---
title: mysql ERROR 1101 (42000)
tags: [debug,mysql]
date: 2018-09-29 17:24:22
permalink:
categories:
description:
---
<p class="description">新建数据表时出现不允许缺省值的报错</p>

<!-- more -->

## 问题描述

新建数据表时出现报错

```mysql
ERROR 1101 (42000): BLOB, TEXT, GEOMETRY or JSON column 'description' can't have a default value
```

这是由于mysql处于strict mode, 不允许text或blod字段有缺省值。因此，我们需要修改mysql为非严格模式。

```mysql
>show variables like 'sql_mode';
| Variable_name | Value |
sql_mode
ONLY_FULL_GROUP_BY, STRICT_TRANS_TABLES, NO_ZERO_IN_DATE, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO, NO_AUTO_CREATE_USER, NO_ENGINE_SUBSTITUTION
```

## 解决方案

一般建议修改mysql的配置文件`my.ini`,将其中`sql-mode`这一行注释掉即可。
但在LinuxMint中，mysql的默认配置文件中没找到这一行。因此，手动在mysql中修改`sql_mode`

```mysql
>set session sql_mode='ONLY_FULL_GROUP_BY,NO_ZERO_IN_DATE,NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO';
```

## 参考来源

https://yq.aliyun.com/sqlarticle/35671
https://segmentfault.com/a/1190000004512672

<hr />