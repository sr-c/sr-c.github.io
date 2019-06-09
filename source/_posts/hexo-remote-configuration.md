---
title: hexo的多端配置
date: 2018-01-08 21:59:17
tags: Hexo,github
---
想要多设备配置hexo，中间遇到了几个坑，记录下来，也帮助大家。

## 背景介绍

>hexo会将个人写的文章经过编译在public/.deploy_git文件夹中，然后将编译好的静态站点文件上传到github。因此，github中默认只存在已编译好的静态站点文件，不存在hexo编译环境，使得作者不能够直接在异地发布文章。

## 中心思想

>在github库中另建立一个分支用于存储hexo的编译环境。作者在异地先通过git同步hexo编译环境，再通过hexo发布文章即可。

<!-- more -->
## 具体步骤

>前提：笔者已参照[官网说明](https://hexo.io/zh-cn/docs/index.html)在github上配置好了个人站点。（设置好了主题，写了几篇文章）

### 1. 在github上新建分支hexo用于存储hexo的配置环境，并将其设置为默认分支。

### 2. 在本地博客根目录下使用git命令将hexo配置环境上传到github上新建的hexo分支
```bash
git init  // git初始化
git remote add origin https://github.com/用户名/仓库名.git  // 添加仓库地址
git checkout -b hexo  // 新建分支hexo并切换到新建的分支hexo
git add .  // 添加所有本地文件到git
git commit -m "更新说明"  // git提交
git push origin hexo  // 文件推送到hexo分支
```

### 3. 在另一设备上使用git命令下载hexo分支中的文件
```bash
// 克隆文件到本地
git clone -b 分支名 https://github.com/用户名/仓库名.git
```

### 4. 在另一设备上（已安装好git与hexo）即可开始对站点的管理
```bash
cd <folder>
npm install
hexo s
```

## 注意事项

>* 在不同设备上配置github注意要先配置好ssh密钥
>* `themes`文件夹中的主题如果是直接`git clone`下来的，在上传前先删除相应主题目录下的`.git`文件夹，但仍不能取得相应主题文件夹的权限。[解决方案](http://blog.csdn.net/sinat_27088253/article/details/54314742)是将相应文件夹剪切到别处，执行一遍`add commit push`(在github端删除了该文件夹)，再在本地粘帖回来，执行`add commit push`三连，重新上传，即可。
>* 新配置的设备上，安装好sshkey后仍然提示`Host key verification failed.  fatal: Could not read from remote repository.`，则需要手动连接一次`ssh git@github.com `即可。

## 参考来源
https://www.zhihu.com/question/21193762
http://blog.csdn.net/Monkey_LZL/article/details/60870891
http://blog.csdn.net/sinat_27088253/article/details/54314742
