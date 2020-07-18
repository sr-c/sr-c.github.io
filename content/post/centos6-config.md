---
title: CentOS6安装后配置
tags: [CentOS]
date: 2019-01-30 18:33:06
permalink:
categories:
description:
---
<p class="description">CentOS6在老爷机上Desktop安装后，进行的一点配置</p>

<!-- more -->

## 配置中文支持
```bash
yum groupinstall "Chinese support" -y
```

## 安装git
yum提供的`git`版本为1.7.1，推荐使用第三方源提供的版本，或自行编译。
```bash
#yum remove git
#yum -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
#yum -ivh https://centos6.iuscommunity.org/ius-release.rpm
#yum --disablerepo=* --enablerepo=ius install git
wget https://www.kernel.org/pub/software/scm/git/git-2.20.1.tar.xz
tar Jxf git-2.20.1.tar.xz
cd git-2.20.1
make prefix=/usr/local/git all
sudo make prefix=/usr/local/git install

echo 'export PATH=$PATH:/usr/local/git/bin' >> ~/.bashrc
source ~/.bashrc
git --version
```

## 安装node.js
```bash
wget https://nodejs.org/dist/v4.4.4/node-v4.4.4-linux-x64.tar.xz
tar Jxf node-v4.4.4-linux-x64.tar.xz
mv node-v4.4.4 /opt/

echo 'PATH=$PATH:/opt/node-v4.4.4' >> ~/.bashrc
source ~/.bashrc
```

## 安装hexo
```bash
git clone -b hexo https://github.com/用户名/仓库名.git
cd <folder>
npm install -g hexo-cli
npm install
hexo s
```

## 参考来源
https://www.cnblogs.com/007sx/p/6076151.html

<hr />
