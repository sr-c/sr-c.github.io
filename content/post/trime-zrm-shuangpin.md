---
title: "同文输入法(Trime)在Android上使用自然码双拼"
date: 2021-07-01T19:10:49+08:00
tags: [rime, Android]
categories: []
draft: false
---

在Android上使用双拼输入法，那么trime可能是你最佳的选择。

<!-- more -->

近年来，许多大厂的输入法都存在这样或那样的隐私数据收集记录。6月11日，搜狗输入法、讯飞输入法、QQ输入法因未完全满足5月1日国家网信办[关于个人信息收集违规问题通报](http://www.cac.gov.cn/2021-04/30/c_1621370239178608.htm)的整改要求，被多家应用商店下架。因此，如果注重自生的隐私数据，输入法这部分，应当注重。

## 安装与配置

直接在[github](https://github.com/osfans/trime/releases/latest)上下载安装包，或者从[F-Driod,](https://f-droid.org/zh_Hans/packages/com.osfans.trime/) [Play Store](https://play.google.com/store/apps/details?id=com.osfans.trime)安装。

与Rime相同，Trime的配置也是通过配置文件完成。推荐在PC上编辑这些文件，之后覆盖至手机的配置目录(默认路径为`/sdcard/rime`)。

Rime的主要配置文件如下

- default.yaml —— 各方案共享的全局配置
- default.custom.yaml —— （可选）对 default.yaml 的「补丁」
- XXX.schema.yaml —— XXX 输入方案的配置
- XXX.custom.yaml —— （可选）对 XXX.schema.yaml 的「补丁」
- XXX.dict.yaml —— XXX 输入方案的词库（字典）

可直接移植使用自然码双拼[方案](https://github.com/mutoe/rime)。该方案能够使用辅码，以及拆字反查，并附带了许多词库。

## 键盘布局

不同于PC端，手机端输入法的键盘布局尤为重要，直接影响输入体验。而Trime的键盘布局UI相关的配置文件为`trime.yaml`, 用户自定义的相应文件为`trime.custom.yaml`。Trime项目中有详尽的[说明文档](https://github.com/osfans/trime/wiki/trime.yaml%E8%A9%B3%E8%A7%A3)，默认的[配置文件](https://github.com/osfans/trime/blob/v3.0.1/assets/rime/trime.yaml)中的注释也很丰富。

此处采用wzyboy自定义的类Google Pinyin界面，相应的配置文件[在此](https://github.com/wzyboy/rime_config/blob/94be9ce8856502deea62d508d169515fc7777599/trime.custom.yaml#L8-L95)。

## 参考来源

https://wzyboy.im/post/1251.html

https://github.com/osfans/trime

