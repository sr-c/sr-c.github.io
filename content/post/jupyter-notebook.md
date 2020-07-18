---
title: 在服务器安装Jupyter Notebook
tags: [jupyter]
date: 2018-09-19 22:02:31
permalink:
categories:
description:
---
<p class="description">使用Jupyter Notebook作为实验记录本，实时记录实验过程</p>

<!-- more -->

## 安装

使用conda安装简单方便

```bash
conda creat -n jupyter python=3.6
source activate jupyter
conda install jupyter notebook
```

## 配置

```bash
jupyter notebook --generate-config	##生成配置文件
jupyter notebook password	##设置jupyter密码
vi ~/.jupyter/jupyter_notebook_config.py
```

设置如下

```bash
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8889	##手动设置一个空闲的端口
c.NotebookApp.notebook_dir = u'/home/user1/jupyter'  ##设置jupyter的工作目录
```

## 启动

后台启动Jupyter Notebook

```bash
nohup jupyter notebook > ~/jupyter/jupyter.log 2>&1 &
```

## 其他

服务器的防火墙没有开放我们所需要的端口，不能够直接远程访问服务器的目的端口

### 方法一

此时，我们可以在本地建立ssh隧道，进行连接

```bash
ssh -N -f -L localhost:8000:localhost:8889 -p 22 username@host_ip
```

连接成功后，在本机浏览器访问

```bash
localhost:8000
```

在网页输入之前设置的jupyter密码即可访问服务器端的Jupyter Notebook

### 方法二

此外，我们也可以手动开放防火墙的指定端口

```bash
iptables -I INPUT -p tcp --dport 8889 -j ACCEPT
iptables save
```

## 参考来源

https://jupyter-notebook.readthedocs.io/en/latest/public_server.html

http://blog.biochen.com/archives/1169

https://blog.csdn.net/tuzixini/article/details/79105482

https://zhuanlan.zhihu.com/p/20226040

<hr />