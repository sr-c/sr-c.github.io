---
title: Windows中R的配置
tags: [R]
date: 2019-03-16 22:53:20
permalink:
categories:
description:
---
<p class="description">在Windows中配置R</p>

<!-- more -->

## 用户目录

R的用户目录在Windows系统下默认位于当前用户的Documents文件夹

```R
> normalizePath('~')
[1] "C:\\Users\\yourUserName\\Documents"
```

> 注意，若你使用了Onedrive，并且将文档文件夹加入了自动保护，那么当前用户的Documents文件夹将会被导向至OneDrive文件夹，导致莫名其妙的错误。[建议将该功能关闭](https://support.office.com/zh-cn/article/%E4%B8%8E-onedrive-%E5%90%8C%E6%AD%A5-%E6%96%87%E6%A1%A3-%E3%80%81-%E5%9B%BE%E7%89%87-%E5%92%8C-%E6%A1%8C%E9%9D%A2-%E6%96%87%E4%BB%B6%E5%A4%B9-d61a7930-a6fb-4b95-b28a-6552e77c3057)。

## 配置文件

### `.Renviron`

该文件设置R自身的一些环境变量，仅对R有效，不改变操作系统的设置。

```R
file.edit('~/.Renviron')
```

推荐在此设置`R_LIBS_USER`，指定R的附加包安装目录。

```
R_LIBS_USER="~/R"
```

若该目录不存在，需要自行手动创建。

```R
dir.creat('~/R')
```

### `.Rprofile`

该文件其实是一个R代码文件，在R启动时，该文件会被首先执行。因此，我们可以将一些个人选项写在其中。

```R
file.edit('~/.Rprofile')
```

使用`options()`函数设置R运行时的一些选项，如CRAN的镜像地址

```
options("repos" = c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/",
CRANextra = "http://www.stats.ox.ac.uk/pub/RWin"))
```

## `PATH`环境变量

> “我的电脑”（右键）–>“属性”–>“高级”–>“环境变量”–>“系统变量”–>PATH

其实R在安装的时候，默认会将配置写入注册表

## `Rconsole`

该文件是R在Windows下独有的一个配置文件，可以直接使用文本编辑器编辑。其中配置了Windows下R界面的属性，如界面语言或字体等。

```R
file.path(R.home('etc'), 'Rconsole')
```

```
language = en # 修改界面语言为英文
```

## 升级方式

Windows下R的升级方式很不灵活，官方推荐的[方式](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#What_0027s-the-best-way-to-upgrade_003f)竟然是重新安装，手动复制附加包到新的library文件夹。

实测我们可以先卸载旧版本的R，卸载程序会保留library文件夹。然后再安装新版本R，手动指定安装目录至原路径。最后再新版本的R中运行

```R
update.packages(checkBuilt=TRUE, ask=FALSE)
```

由于之前的配置文件如`.Renviron`, `.Rprofile`等存在于用户目录下，升级过程中不受影响。

## 参考来源

https://bookdown.org/yihui/r-ninja/setup.html#configure-r

<hr />