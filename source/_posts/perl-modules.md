---
title: 配置perl模块
tags: [perl]
date: 2018-05-27 22:50:40
permalink:
categories:
description:
image:
---
<p class="description">普通用户手动配置perl模块可谓噩梦，选择一款趁手的工具很有必要。使用CPAN配置模块依然不够简单，目前尝试Perlberw与cpanminus的组合，较为满意。</p>

<!-- more -->

### Perlberw

```shell
\curl -L https://install.perlbrew.pl | bash
```

安装完成后即可不需`sudo`安装模块，尝试不同版本的perl

### cpanm管理模块

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

<hr />