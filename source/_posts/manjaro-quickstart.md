---
title: 在manjaro上配置工作环境
tags: [manjaro,arch,linux]
date: 2019-05-11 19:48:51
permalink:
categories:
description:
---
<p class="description">在manjaro上配置工作环境</p>

<!-- more -->

最近看老莱视频，了解到用户友好的`manjaro`(竟然内置steam), 再加上强大的AUG, 忍不住来尝试一下。

## 安装

使用`dd`将安装镜像写入到u盘

```bash
#确认u盘的挂载位置，写入前取消挂载
df -h
umount /dev/sdx
#使用dd命令写入
dd if=path/to/manjaro.iso of=/dev/sdx bs=4M status=progress
```

系统安装过程非常方便，`manjaro`的安装引导非常友好，在分区过程的提示很细致，还会提示每个磁盘的分区表类型。

最简单安装只需指定efi的引导分区`/boot/efi`和`/`挂载点即可

> 由于 Windows10 本身的引导分区就是 ESP 分区，所以到分区的界面，只需要将 /boot/efi 挂载到 ESP 分区即可。

## 软件源配置

安装时语言设置为en.utf-8, 时区设置为上海，默认就安装了中文支持。

### 软件源

```bash
#选择最快的镜像源
sudo pacman-mirrors -i -c China -m rank
#刷新缓存
sudo pacman -Syy
```

### 添加ArchLinuxCN源

```bash
#在文件/etc/pacman.conf末尾添加如下内容
sudo echo '[archlinuxcn]\nSigLevel = Optional TrustedOnly\nServer = https://mirrors.ustc.edu.cn/archlinuxcn/$arch' >> /etc/pacman.conf
#导入archlinuxcn-keyring
sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
#再次刷新缓存，下载速度应该就比较令人满意了
sudo panman -Syy
```

## 时间设置

对于`Windows`与`linux`双系统的用户，系统时间的设置往往是一个比较头疼的问题。

### 问题原因

```bash
timedatectl
```

可以看到，系统时间实际分为`Local time`,`Universal time`与`RTC time`

Windows使用的是本地时间`Local time`, 而UNIX使用的是UTC时间`Universal time`. 

Windows会认为主板BIOS上的硬件时间就是本地时间，同步时间后也会将时间写入BIOS. UNIX则认为硬件时间是UTC时间，同步时间后会将本地时间按照时区计算后得到的UTC时间写入主板。

这样，在一个已有Windows的电脑上，BIOS的硬件时间就会被写为本地时间。之后安装的UNIX会将BIOS上的硬件时间识别为UTC时间，再根据时区信息计算出本地时间，显示在系统上。因此，我们会看到UNIX默认显示的时间与实际本地时间相差8个小时。

### 解决方案

因此，该问题就存在两种解决方案。一是让Linux使用本地时间`local time`, 或是让Windows使用UTC时间。以下方案择一即可。

#### Linux方案

修改为本地时间（目前使用此方案）

```bash
timedatectl set-local-rtc 1 --adjust-system-clock
timedatectl set-ntp 0
```

[使用NTP同步时间](https://alexiachen.github.io/blog/2018/08/11/manjaro-hello/)

```bash
sudo ntpdate cn.ntp.org.cn #可以换成任何一个公开的NTP Server地址
```

#### Windows方案

修改注册表，且需关闭时间同步。（未执行）

> [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation]
>
> "RealTimeIsUniversal"=dword:00000001

## 常用软件

### 安装fcitx

```bash
sudo pacman -S fcitx-googlepinyin
sudo pacman -S fcitx-im
sudo pacman -S fcitx-configtool

#在桌面环境配置文件中写入相关设置
echo "export GTK_IM_MODULE=fcitx\nexport QT_IM_MODULE=fcitx\nexport XMODIFIERS=@im=fcitx" >> ~/.xprofile
```

注销后即可正常使用fcitx

> 若初始选择安装的英文系统，再安装fcitx可能会使得fcitx中的中文输入法不能正常调用，此时应修改本地化设定中的语言

### 安装yay

```bash
sudo panman -S yay
```

由于`yaourt`目前已停止开发，存在安全问题，故选择`yay`进行包管理

### 其他软件

```bash
yay -S wps-office nutstore typora sublime-text-imfix visual-studio-code-bin
```

## 其他设置

### 字体配置

`XFCE`桌面默认的字体配置在`外观`中设置。默认字体为`Noto Sans Regular`, 默认等宽字体为`Monospace Regular`, 已有不错的显示效果。

字体大小默认为10号字，根据屏幕情况可自行调整至12或14号字。

### 自启动配置

配置文件位于`～/.config/autostart/`目录下，也可通过`会话和启动`进行设置。

参考坚果云写入的自启动方式，可自行将启动项写入为

```bash
sh -c "(sleep 30 && nohup /opt/nutstore/bin/nutstore-pydaemon.py >/dev/null 2>&1) &"
```

## 参考来源

https://wenqixiang.com/manjaro-guide/

https://www.lanbu.net/d/52/11

http://blog.sina.com.cn/s/blog_18373c26b0102xo4g.html

https://blog.csdn.net/aaazz47/article/details/78696899

<hr />