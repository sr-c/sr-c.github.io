---
title: Docker初探
tags: [Docker]
date: 2019-07-19 21:04:02
permalink:
categories:
description:
---
<p class="description">9012年了，得学习点Docker了</p>
<!-- more -->

## 安装

```bash
sudo pacman -S docker
```

### 为普通用户配置权限

#### 创建`docker`用户组

```bash
sudo groupadd docker
```

#### 将当前用户添加到`docker`用户组中

```bash
sudo usermod -aG docker $USER
```

#### 注销后重新登录，或直接使修改生效

```bash
newgrp docker
```

#### 验证

```bash
docker run hello-world
```

### 设置镜像加速

对于使用`systemd`的系统，新建`/etc/docker/daemon.json`，并写入

```json
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```

然后，重启服务

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 使用nginx服务

[小试牛刀](https://juejin.im/post/5c2c69cee51d450d9707236e#heading-4)，使用docker提供的nginx服务

```bash
docker run -d -p 80:80 --restart=always nginx:latest
```

后台运行的Docker中的nginx就指向的本机的80端口。打开http://localhost:80就可打开nginx的欢迎页。

查看已有的容器

```bash
docker container ls --all
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS                NAMES
b37e7689fdfe        nginx:latest        "nginx -g 'daemon of…"   3 minutes ago       Up 3 minutes                     0.0.0.0:80->80/tcp   competent_mirzakhani
dfe9c2dbb13b        hello-world         "/hello"                 3 hours ago         Exited (0) 3 hours ago                                upbeat_lederberg
```

可以看到每个容器对应的唯一ID

直接修改欢迎页的内容

```bash
docker exec -it b37e7689fdfe bash

echo '<h1>Hello Docker<h1/>' > /usr/share/nginx/html/index.html
```

然后`Ctrl + D`或`exit`退出容器，刷新http://localhost:80即可看到修改后的主页。

## 参考来源

https://docker_practice.gitee.io/

https://docs.docker.com/get-started/

https://juejin.im/post/5c2c69cee51d450d9707236e

https://jimolonely.github.io/2018/04/02/cloudcompute/004-docker/

<hr />