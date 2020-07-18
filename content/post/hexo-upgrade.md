---
title: Hexo升级
tags: [node.js,Hexo,Next]
date: 2019-06-05 18:01:20
permalink:
categories:
description:
---
<p class="description">Hexo升级时需要注意调整配置文件</p>

<!-- more -->

# 升级方案

## node.js

### 使用n模块管理node版本

```bash
#查看当前node版本
node -v
#清除npm chche
npm cache clean -f
#安装n模块
npm install -g n
#使用n模块安装最新稳定版
n stable
#或者使用n模块安装指定版本
n 8.1.2
```

Windows下还可以以安装包直接覆盖安装，进行升级。

```bash
#查看node安装路径
where node
```

## Hexo

直接使用`npm i hexo-cli -g` 和 `npm update`升级并不完整。正确的姿势是

```bash
#安装npm-check与npm-upgrade
npm install -g npm-check
npm install -g npm-upgrade
#检查并更新
npm-check
npm-upgrade
#一路回车确认安装即可
#在本地生产环境安装
npm install --save
```

最后一步的作用如下

- 会把依赖包安装到 node_modules 目录中
- 会在package.json的dependencies属性下添加依赖包的名称和版本号
- 之后运行npm install命令时，会自动安装依赖包到node_modules目录中

查看`package.json`中的`dependencies`属性可确认更新。

## Next

Hexo主题的[更新说明](https://github.com/theme-next/hexo-theme-next#update)是直接通过`git pull`更新，但是跨度较大时，你很可能会发现配置文件挂掉。因此，我们需要采取单独的配置[策略](https://github.com/theme-next/hexo-theme-next/blob/master/docs/DATA-FILES.md#option-2-next-way)

简单来说，就是将站点和主题的配置一起放置在`source/_data/next.yml`，而不需要单独去管理站点配置`_config.yml`和主题配置`theme/next/_config.yml`

首先，确认主题默认配置`theme/next/_config.yml`中的`override`设置为`false`。然后，将站点配置`_config.yml`的**全部**内容和主题配置`theme/next/_config.yml`中**自定义**的部分，整合到`source/_data/next.yml`，以后该文件即可完成所有的配置。

## 参考来源

https://juejin.im/post/5c09e47ee51d45721d71087d

https://hexo.imydl.tech/archives/51612.html

<hr />