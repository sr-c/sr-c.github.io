---
title: SCP遇到"Write failed Broken pipe"
tags: [ssh,scp,debug]
date: 2018-08-14 11:51:50
permalink:
categories:
description:
---
<p class="description">使用`scp`在服务器间传输文件时出现`ETAWrite failed: Broken pipe`错误，同时报连接断开。</p>

<!-- more -->

## 解决方案

无管理员权限则修改客户端的ssh配置

```bash
##编辑ssh配置文件，无此文件则新建
vi ~/.ssh/config
##在其中添加如下语句
ServerAliveInterval 60
```

使得客户端在ssh连接时，每隔60秒发送一个keep-alive包

## 使用rsync下载

虽然通过上述设置能够避免长时间传输文件时`scp`异常掉线的问题，但对于大文件传输，仍不能实现断点续传，这时就需要使用同步工具`rsync`来进行服务器间的同步了。

```bash
rsync -Rr 文件夹名 ip::目标模块
```
### 选项说明

> -v, --verbose 详细模式输出
-q, --quiet 精简输出模式
-c, --checksum 打开校验开关，强制对文件传输进行校验
-a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD
-r, --recursive 对子目录以递归模式处理
-R, --relative 使用相对路径信息

```bash
rsync foo/bar/foo.c remote:/tmp/
##则在/tmp目录下创建foo.c文件，而如果使用-R参数：
rsync -R foo/bar/foo.c remote:/tmp/
##则会创建文件/tmp/foo/bar/foo.c，也就是会保持完全路径信息。
```

## 参考来源

https://www.logcg.com/archives/897.html

https://www.cnblogs.com/dudu/archive/2013/02/07/ssh-write-failed-broken-pipe.html

http://blog.51cto.com/1inux/1751255

http://www.dahouduan.com/2014/11/19/rsync-daemon/

http://www.cnblogs.com/MikeZhang/p/rsyncExample_20160818.html

https://blog.csdn.net/hepeng597/article/details/8960885

<hr />