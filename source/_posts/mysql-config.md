---
title: 配置MySQL Server
tags: [mysql,debug]
date: 2018-09-09 16:31:04
permalink:
categories:
description:
---
<p class="description">Debian/Ubuntu系统的用户目前在安装MySQL Server 5.7版本后无需设置root密码。因此其使用的root密码都是空的，需要在配置完成后再设置密码。</p>

<!-- more -->

这一情况使得用户使用`mysql -u root -p`登陆时出现`MySql Error 1698(28000)`错误。这与之前出现`MySql Error 1698(28000)`错误的原因不同，因此很多网上`MySql Error 1698(28000)`问题的解决方案不奏效。

使用linuxMint安装MySQL Server 5.7, 在导入本地blast2go数据库时出现错误。

## MySql Error 1698(28000)

### 表征

```bash
mysql -u root -p
Enter password: 
ERROR 1698 (28000): Access denied for user 'root'@'localhost'(using password: NO)
```

### 进一步确认

默认root用户的plugin项为`auth_socker`，不是`mysql_native_password`，因此不能够输入密码进入。同时，也可以看到root用户的密码为空。

```bash
sudo mysql -u root	##如此方可进入刚安装的MySQL
```

```mysql
mysql> USE mysql;
mysql> SELECT User, Host, plugin FROM mysql.user;
+------------------+-----------------------+
| User             | plugin                |
+------------------+-----------------------+
| root             | auth_socket           |
| mysql.sys        | mysql_native_password |
| debian-sys-maint | mysql_native_password |
+------------------+-----------------------+
mysql> SELECT User, Host, HEX(authentication_string) FROM mysql.user;
+-----------+-----------+-------------------------------------------------------------+
| User      | Host      | HEX(authentication_string)                                  |
+-----------+-----------+-------------------------------------------------------------+
| root      | localhost |                                                             |
```

> As you can see in the query, the root user is using the auth_socket plugin.And the password is empty.

## 解决方案

### Step 1 

#### Option 1: set the root user to use the mysql_native_password plugin

```bash
$ sudo mysql -u root # I had to use "sudo" since is new installation

mysql> USE mysql;
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
mysql> FLUSH PRIVILEGES;
mysql> exit;

$ service mysql restart
```

#### Option 2: replace YOUR_SYSTEM_USER with the username you have

```bash
$ sudo mysql -u root # I had to use "sudo" since is new installation

mysql> USE mysql;
mysql> CREATE USER 'YOUR_SYSTEM_USER'@'localhost' IDENTIFIED BY '';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'YOUR_SYSTEM_USER'@'localhost';
mysql> UPDATE user SET plugin='auth_socket' WHERE User='YOUR_SYSTEM_USER';
mysql> FLUSH PRIVILEGES;
mysql> exit;

$ service mysql restart
```

Remember that if you use option #2 you'll have to connect to mysql as your system username (mysql -u YOUR_SYSTEM_USER)

Note: On some systems (e.g., Debian stretch) 'auth_socket' plugin is called 'unix_socket', so the corresponding SQL command should be: UPDATE user SET plugin='unix_socket' WHERE User='YOUR_SYSTEM_USER';

### Step 2 向`root`用户添加密码

```mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_password';
mysql> SELECT User, Host, HEX(authentication_string) FROM mysql.user;   ##查看密码是否添加成功
```

### 终极一键解决方案

```mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
```

And now, it works 🙂

```mysql
mysql> SELECT User, Host, HEX(authentication_string) FROM mysql.user;
+-----------+-----------+------------------------------------------------------------------------------------+
| User      | Host      | HEX(authentication_string)                                                         |
+-----------+-----------+------------------------------------------------------------------------------------+
| root      | localhost | 2A39344244434542453139303833434532413146393539464430324639363443374146344346433239 |
```

## 重新安装mysql

对于5.5版本生效，但对于5.7版本MySQL，重新安装的过程中仍然不会向用户要求设置root密码，因此并不奏效。

```bash
sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-5.5 mysql-client-core-5.5
sudo rm -rf /etc/mysql /var/lib/mysql
sudo apt-get autoremove
sudo apt-get autoclean
```

再重新安装mysql-server，此时可在安装时设置root密码

```bash
sudo apt-get update
sudo apt-get install mysql-server
```

## 参考来源

https://www.percona.com/blog/2016/03/16/change-user-password-in-mysql-5-7-with-plugin-auth_socket/
https://stackoverflow.com/questions/39281594/error-1698-28000-access-denied-for-user-rootlocalhost

<hr />