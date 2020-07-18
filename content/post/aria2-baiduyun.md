---
title: 百度云的aria2下载
tags: [aria2]
date: 2018-07-04 17:19:25
permalink:
categories:
description:
---
<p class="description">使用aria2下载百度云文件，绕过限速</p>

<!-- more -->

## 下载aria2

```bash
https://github.com/aria2/aria2/releases/download/release-1.34.0/aria2-1.34.0.tar.gz
```

## 编译安装

```bash
./configure --prefix=~/opt/aria2
make
make install
```

## 浏览器附加组件

Firefox https://addons.mozilla.org/zh-CN/firefox/addon/baidu-pan-exporter/

Chrome https://github.com/acgotaku/BaiduExporter

## aria2配置文件

```bash
mkdir download
cd download
touch aria2.session aria2.log
vi aria2.conf
```
```bash
## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
dir=~/download/
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
file-allocation=none
# 断点续传
continue=true

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
max-concurrent-downloads=10
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=5
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
split=20
# 整体下载速度限制, 运行时可修改, 默认:0
#max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
#max-download-limit=0
```

之后即可使用`aria2c --conf-path=<PATH>`启动，其中`PATH`必须为绝对路径。

## 参考来源

https://github.com/acgotaku/BaiduExporter

https://blog.csdn.net/gatieme/article/details/44782801

http://www.bioinfo-scrounger.com/archives/543

<hr />