---
title: 解压缩rar文件
tags: [rar,7zip]
date: 2019-02-06 18:14:47
permalink:
categories:
description:
---
<p class="description">使用7zip解压缩rar格式文件</p>
<!-- more -->
由于rar格式的版权问题，linux发行版的默认源中一般不会提供解压`rar`文件的解决方案。但是，我们可以通过添加第三方软件源（EPEL/RPMforge）来解决这个问题。
## 安装p7zip
```bash
# CentOS通过yum安装
yum install p7zip
# 默认解压至当前目录
7za x myfile.rar
# debian系则通过apt安装
#apt-get install p7zip
#7zr x myfile.rar
```
## 安装unrar
我们还可以通过自行编译`unrar`来解压`rar`文件。
```bash
## go to http://rarlab.com/ get the lastest version
wget http://www.rarlab.com/rar/unrarsrc-5.3.11.tar.gz
tar zxf unrarsrc-5.3.11.tar.gz
cd unrar
make DESTDIR=$HOME all
make DESTDIR=$HOME install-unrar
echo 'PATH=$PATH:$HOME/bin' >> ~/.bashrc
source ~/.bashrc
```
## 参考来源
https://www.centos.org/forums/viewtopic.php?t=5715
https://unix.stackexchange.com/questions/246535/how-to-open-rar-file-in-linux
<hr />
