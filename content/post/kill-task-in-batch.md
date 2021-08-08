---
title: "批量中止进程"
date: 2020-09-02T22:10:44+08:00
tags: [linux]
categories: []
draft: false
---

批量搜索并杀死目标进程

<!-- more -->

```bash
## 搜索当前进程 | 关键词过滤 | 获取pid | 批量运行kill -9
ps -ef | grep "key words" | awk '{print $2}' | xargs kill -9
```

## 批量杀死同一个会话下的进程

```bash
pkill -s <SID>
```

## 杀死父进程与子进程

以SIGHUP信号杀死父进程后，子进程也会被杀死。

## 参考来源

https://leokongwq.github.io/2017/08/22/linux-kill-parent-and-child-process.html

https://blog.csdn.net/aaashen/article/details/50667533

https://juejin.im/post/6844903927989665806

http://morningcoffee.io/killing-a-process-and-all-of-its-descendants.html