---
title: Hexo与NexT升级
date: 2018-05-13T18:32:53.000Z
tags:
  - blog
---
近来发现NexT主题升级到了6.2.0，主仓库由`iissnan`迁移到了`theme-next`，为了更新页面效果，合并站点与主题的配置文件，于是计划升级一波。
> 升级目标
* NexT版本由5.1.3升级至6.2.0
* Hexo版本由3.4.4升级至3.7.1

<!-- more -->
## 升级Hexo
```
$ npm -version
5.6.0
```
由于NexT6.2.0需求Hexo版本至少为3.5.0，因此首先升级Hexo，通过`hexo version` 查看当前版本（需在Hexo工作目录下进行）
```
$ hexo version
hexo: 3.4.4
hexo-cli: 1.1.0
os: Windows_NT 10.0.16299 win32 x64
http_parser: 2.7.0
node: 8.9.4
v8: 6.1.534.50
uv: 1.15.0
zlib: 1.2.11
ares: 1.10.1-DEV
modules: 57
nghttp2: 1.25.0
openssl: 1.0.2n
icu: 59.1
unicode: 9.0
cldr: 31.0.1
tz: 2017b
```
然后查看可升级的组件
```
$ npm outdated
Package                 Current  Wanted  Latest  Location
hexo                      3.4.4   3.7.1   3.7.1  hexo-site
hexo-generator-archive    0.1.4   0.1.5   0.1.5  hexo-site
hexo-renderer-marked      0.3.0   0.3.2   0.3.2  hexo-site
hexo-server               0.2.2   0.2.2   0.3.2  hexo-site
```
以此修改`package.json`，将hexo和相关待更新组件的版本号更新。修改完成后通过npm更新`npm install --save`
```
$ npm install --save
npm WARN deprecated titlecase@1.1.2: no longer maintained

> nunjucks@3.1.2 postinstall C:\Users\Yaman\sr-c.github.io\node_modules\nunjucks
> node postinstall-build.js src

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.3 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.3: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

added 44 packages, removed 78 packages, updated 21 packages and moved 1 package in 12.845s
```
再次通过`hexo version`查看版本，更新完成
```
$ hexo version
hexo: 3.7.1
hexo-cli: 1.1.0
os: Windows_NT 10.0.16299 win32 x64
http_parser: 2.7.0
node: 8.9.4
v8: 6.1.534.50
uv: 1.15.0
zlib: 1.2.11
ares: 1.10.1-DEV
modules: 57
nghttp2: 1.25.0
openssl: 1.0.2n
icu: 59.1
unicode: 9.0
cldr: 31.0.1
tz: 2017b
```
## 升级NexT
参照[官方文档](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md)，重新为新版本NexT配置一个目录，相当于重新安装一个新的主题。
同时，参照NexT的建议，将配置文件放置在站点目录的`/source/_data/next.yml`中。
> 注意——语言配置
NexT新版本中将`zh-Hans`改为了更为通用的`zh-CN`，注意在配置文件中[更正](https://github.com/theme-next/hexo-theme-next/commit/7214c2c0fc0b441231df9fefc7190c5fb37d66f4)，否则语言配置无效。

参考来源：
https://tommy.net.cn/2018/02/26/upgrade-hexo-to-v3-5-0/
http://zerosoul.github.io/2016/06/15/upgrade-hexo-to-3-2/

