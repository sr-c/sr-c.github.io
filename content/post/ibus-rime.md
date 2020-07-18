---
title: 使用ibus输入法
tags: [ibus,rime]
date: 2019-12-21 19:21:39
permalink:
categories:
description:
---
<p class="description">linux下使用fcitx输入法一直很方便，但是在一些特定环境(如Rstudio)中还是存在问题，改用ibus就能很好解决问题。</p>

<!-- more -->

在`manjaro`中替换`fcitx`输入法为`ibus`, 设置启动项，并配置环境调用。

## 安装

```bash
yay -S ibus
yay -S ibus-rime
```

## 配置启动项

### 添加ibus启动项

在`/etc/xdg/autostart`（或`~/.config/autostart`）路径中新建一个可执行文件`ibus.desktop`, 内容如下:

```bash
[Desktop Entry]
Exec=ibus-daemon -xdr
GenericName=IBus
Name[zh_CN]=IBus
Name=IBus
Name[en_US]=IBus
StartupNotify=true
Terminal=false
Type=Application
```

并设置可执行权限

```bash
chmod 755 ibus.desktop
```

### 更改fcitx启动项

`fcitx`的启动项可能会在`/etc/xdg/autostart`或`~/.config/autostart`中，注意删除或重命名备份之。

## 配置与其他应用配合

在`/etc/profile`或`~/.xprofile`中加入以下内容

```bash
export GTK_IM_MODULE=ibus
export QT_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
```

## 配置rime输入法

`RIME`输入法，准确地说是RIME输入法引擎，是“聪明的输入法懂我心意”。简单配置以下就可快速上手。

### 切换繁简中文输入

按组合键`Ctrl +`  或`F4`即可呼出输入方案选项。第一栏为目前正在应用的输入法，第二栏为目前输入法的设置，之后的选项为其他可选输入法。

在第二栏中，选择4，将默认的繁体输入改为简体即可。此外，其中还可配置全角半角等。

### 配置双拼

默认的【朙月拼音】中存在几个流行的双拼方案：自然码、MSPY、智能ABC、小双拼、拼音加加，在`~/.config/ibus/rime`中新建配置文件`default.custom.yaml`, 在其中写入

```bash
patch:
  schema_list:
    - schema: luna_pinyin
    - schema: double_pinyin
```

默认方案似乎就是自然码。

### 详细配置

参考配置文件[说明](https://github.com/rime/home/wiki/RimeWithSchemata)

## 参考来源

https://www.lulinux.com/archives/5297

https://www.linuxsecrets.com/archlinux-wiki/wiki.archlinux.org/index.php/Rime_IME.html

<hr />