---
title: 重装mysql
tags: [mysql]
date: 2018-09-11 22:36:17
permalink:
categories:
description:
---
<p class="description">对着MySQL一整折腾之后，还是重装吧😂</p>

<!-- more -->

重新安装mysql, 将其回复默认的配置

### 删除mysql

```bash
sudo apt-get remove --purge mysql-*
```

### 清理残留数据

```bash
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

或者更为手动的方式，首先搜索含有mysql文件名的路径

```bash
sudo find  / -name mysql -print
```

然后，使用`sudo rm -rf`执行删除

### 安装mysql

```bash
sudo apt-get install mysql-server
```

安装过程中未提示设置管理员密码，因此，可通过如下方式直接登陆mysql

```bash
sudo mysql -uroot
```

但在某些条件下，我们希望设置mysql管理员的密码

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'test'; 
```

> 此处，`test`是为管理员账户设置的新密码
> 如此，mysql即可通过`mysql -uroot -ptest`方式登陆。

## 参考目录

https://www.jianshu.com/p/c76b31df5d09
https://blog.csdn.net/chudongfang2015/article/details/52154903
https://askubuntu.com/questions/1029177/error-1698-28000-access-denied-for-user-rootlocalhost-at-ubuntu-18-04
https://stackoverflow.com/questions/39281594/error-1698-28000-access-denied-for-user-rootlocalhost
https://www.percona.com/blog/2016/03/16/change-user-password-in-mysql-5-7-with-plugin-auth_socket/

<hr />