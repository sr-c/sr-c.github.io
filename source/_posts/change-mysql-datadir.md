---
title: 更改mysql数据库路径
tags: [mysql,datadir]
date: 2018-09-11 22:35:20
permalink:
categories:
description:
---
<p class="description">手动更改mysql的数据库存放路径</p>

<!-- more -->

## 查看mysql文件存放目录

```mysql
show variables like '%dir%';
```

能够查看到目前的数据库文件配置信息，其中的datadir值为`/var/lib/mysql/`

## 停止mysql服务

```bash
sudo service mysql stop
```

## 迁移数据库文件

### 直接移动目录

```bash
sudo mv /var/lib/mysql /mnt/data/
```

### 拷贝目录（同时复制文件权限信息）

```bash
sudo cp -a /var/lib/mysql /mnt/data/
```

## 修改配置文件

### my.cnf

mysql数据库会按照优先级顺序从/etc/my.cnf, /etc/mysql/my.cnf, /usr/etc/my.cnf, ~/.my.cnf四个位置找my.cnf配置文件。Ubuntu默认将my.cnf配置问价放置在/etc/mysql/my.cnf位置，没有/etc/my.cnf文件。
在基于Ubuntu的LinuxMint19中，其/etc/mysql/my.cnf文件内容如下

```
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

将配置指向了同级目录另两个文件夹中的配置文件，其中`/etc/mysql/mysql.conf.d/mysqld.cnf`设置了关键内容，在其[mysql]标签下，将datadir值改为新的路径，保存修改后退出。

### usr.bin.mysqld

由于Ubuntu使用的apparmor安全模块，还需修改apparmor的配置文件`/etc/apparmor.d/usr.sbin.mysqld`找到其中
/var/lib/mysql/ r,
/var/lib/mysql/** rwk,
两行权限声明，修改为：
/mnt/data/mysql/ r,
/mnt/data/mysql/** rwk,

### abstractions/mysql

由于usr.bin.mysqld文件中引用了abstractions/mysql文件，因此还需要修改其中的设置。但在LinuxMint 19 中，并没有这个文件，因此，我们也就不做修改。

## 重启数据库

重新载入apparmor配置文件

```bash
sudo /etc/init.d/apparmor restart
```

再重启mysql即可

```bash
sudo /etc/init.d/mysql start
```

## 参考来源

https://blog.csdn.net/qinxiandiqi/article/details/43270147

<hr />