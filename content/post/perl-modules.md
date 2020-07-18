---
title: 配置perl模块
tags: [perl]
date: 2018-05-27 22:50:40
permalink:
categories:
description:
image:
---
<p class="description">普通用户手动配置perl模块可谓噩梦，选择一款趁手的工具很有必要。使用CPAN配置模块依然不够简单，目前尝试`Perlberw`与`cpanminus`的组合，较为满意。</p>

<!-- more -->

## 安装Perlberw

```shell
\curl -L https://install.perlbrew.pl | bash
```

安装完成后即可不需`sudo`安装模块，尝试不同版本的perl

## 使用cpanminus管理模块

使用perlberw安装cpanm

```shell
perlbrew install-cpanm
```

使用cpanm安装模块，不需要管理员权限

```shell
$ cpanm Moose
```

使用 cpanm 安装模块，会将模块安装到目前使用版本 Perl 函数库`~/perl5/perlbrew/perls`中

```shell
$ tree ~/perl5/perlbrew/perls/perl-5.14.1/lib | head 
/Users/c9s/perl5/perlbrew/perls/perl-5.14.1/lib
├── 5.14.1
│   ├── AnyDBM_File.pm
│   ├── App
│   │   ├── Cpan.pm
│   │   ├── Prove
│   │   │   ├── State
│   │   │   │   ├── Result
│   │   │   │   │   └── Test.pm
│   │   │   │   └── Result.pm
```

## 使用local::lib

安装了`perlbrew`与`cpanm`之后，直接使用`cpanm`安装软件会提示

```bash
!
! Can't write to /usr/local/share/perl5 and /usr/local/bin: Installing modules to /home/username/perl5
! To turn off this warning, you have to do one of the following:
!   - run me as a root or with --sudo option (to install to /usr/local/share/perl5 and /usr/local/bin)
!   - Configure local::lib in your existing shell to set PERL_MM_OPT etc.
!   - Install local::lib by running the following commands
!
!         cpanm --local-lib=~/perl5 local::lib && eval $(perl -I ~/perl5/lib/perl5/ -Mlocal::lib)
!
```

若忽略此条，则使得已通过`cpanm`安装的模块不能被正确调用。以`App::pmuninstall`为例，安装后，`pm-unistall`不出现在环境变量中。`local::lib`安装后即可正确调用。

```bash
cpanm --local-lib=~/perl5 local::lib && eval $(perl -I ~/perl5/lib/perl5/ -Mlocal::lib)
```

> p.s. 在PBS集群中，perl的本地配置不能够直接传递到计算节点，使得PASA运行在需调用perl模块时仍然报错。

## perl模块的卸载

使用模块`App::pmuninstall `，可卸载其他模块

```bash
cpanm App::pmuninstall
```

### 手动确认卸载完成

`perl -e 'print join "\n",@INC' `会print出所有模块和库的安装目录，到这些目录下找到对应的pm删除掉就可以了。 

## 参考来源

https://perlbrew.pl/Perlbrew-%E4%B8%AD%E6%96%87%E7%B0%A1%E4%BB%8B.html

https://blog.csdn.net/u011450367/article/details/41522729

https://www.howtoing.com/how-to-install-perlbrew-and-manage-multiple-versions-of-perl-5-on-centos-7

<hr />