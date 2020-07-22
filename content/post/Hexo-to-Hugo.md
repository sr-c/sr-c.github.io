---
title: "将博客从Hexo迁移至Hugo"
tags: [Hugo, Hexo, blog]
date: 2020-07-18T12:07:50+08:00
categories: [Blog]
description:
---

Github最近总是邮件提醒我有些插件过时了，存在安全问题。随着文章数量增多，Hexo的速度越来越慢。因此决定切换到Hugo

<!-- more -->

# 安装

## 安装 Git 和 Go 

Hugo是用Golang编写的，使用Hugo前需要安装[Git](https://git-scm.com/) 和 [Go](https://golang.org/dl/) 语言开发环境。

## 安装Hugo

参考[官方文档](https://gohugo.io/getting-started/installing/#windows)有很多安装方式。对于Windows用户，可直接下载二进制文件，将放置路径添加至环境变量`PATH`即可。

# 生成博客

```bash
hugo version # 查看版本
hugo new site myblog # 使用Hugo新建myblog项目，名称自拟
cd myblog # 进入myblog目录
hugo server # 启动本地调试服务
```

## 安装主题

Hugo没有自带的主题，但其官网的[主题库](https://themes.gohugo.io/)也非常丰富，应该能满足大部分人的需要。安装方式也与Hexo类似，在`themes`目录中`git clone`需要的主题即可。目前使用的是`even`, 推荐的还有`LeaveIt`,`maupassant`

## 配置文件

默认的配置文件为站点目录中的`config.toml`，该文件可直接从主题的`exampleSite/config.toml`直接替换，再进一步自定义。

### 文章迁移

之前Hexo默认的URL为如下形式

```
/2017/01/02/xxxxx
/2017/03/04/yyyyy
/2017/05/06/zzzzz
```

在Hugo的配置文件中，需要做如下设置才能达到相同结构的URL

```toml
[permalinks]
  posts = "/:year/:month/:day/:filename"
```

### Front-matter

Markdown文档开头起说明作用的Front-matter字段应书写规范，Hugo的要求似乎比Hexo更严格，需要检查一下哪些文章没有写好。

1. 在`tags`, `categories`等字段设置了多个值，则应该以` tags: [mongodb, test] `的方式记录。
2. Front-matter字段首尾的`---`分割符不能缺少。

### 其他项目

```toml
## 保持分类的原始名字（false会做转小写处理）
preserveTaxonomyNames = true
## 是否禁止URL Path转小写
disablePathToLower = true
```

### 调试

```bash
hugo server
hugo server -D ##强制渲染非草稿的文章
```

# Github项目迁移

之前，为了Hexo项目的[多地部署](https://sr-c.github.io/2018/01/08/hexo-remote-configuration/)，我在`sr-c.github.io`新开了一个`hexo`分支用于同步Hexo站点的配置目录。每次更新文章，先将站点目录`git push`到`hexo`分支，再使用`hexo g -d`调用`git`将`public`目录的内容提交至`master`分支。

顺应这个思路，我又新建了一个`hugo`分支用于同步站点目录，再将每次更新生成的`public`目录推送至`master`分支。

## 向GitHub添加SSH key

参考之前的[设置](https://www.cnblogs.com/ayseeing/p/3572582.html)方式，对于已有的SSH key，可直接添加至GitHub.

```bash
# Add SSH key to clipboard
clip < ~/.ssh/id_rsa.pub
# Paste the key to GitHub Accout Settings page.
# Test the Key
ssh -T git@github.com
```

## 提交hugo分支

在新建立的Hugo站点目录中

```bash
cd myblog # 位于新建立的站点目录中
git init  # git初始化
git remote add origin https://<USERNAME>/<USERNAME>.github.io.git  # 添加仓库地址
git checkout -b hugo  # 新建分支hexo并切换到新建的分支hexo
git add .  # 添加所有本地文件到git
git commit -m "version_log"  # git提交
git push origin hugo  # 文件推送到hugo分支
```

## 提交master分支

Hugo[官方文档](https://gohugo.io/hosting-and-deployment/hosting-on-github/#step-by-step-instructions)推荐使用[子项目](https://github.blog/2016-02-01-working-with-submodules/)的方式管理并提交`public`目录至`master`分支。

```bash
rm -rf public
#git submodule add -b master https://github.com/<USERNAME>/<USERNAME>.github.io.git public

## deploy.sh
#!/bin/sh

# If a command fails then the deploy stops
set -e

printf "\033[0;32mDeploying updates to GitHub...\033[0m\n"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public

# Ensure submit to object branch
git clone git@github.com:sr-c/sr-c.github.io.git master
# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site $(date)"
if [ -n "$*" ]; then
	msg="$*"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master
```

注意，此处要求先删去已有的`public`目录，但是`public`已被注册称为子项目，最好在`git`中删除该`submodule`，否则如果下一步提交不成功，那么可能会存在问题。删除`submodule`的[方式](https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule/36593218#36593218)记录如下：

```bash
# Remove the submodule entry from .git/config
git submodule deinit -f path/to/submodule

# Remove the submodule directory from the superproject's .git/modules directory
rm -rf .git/modules/path/to/submodule

# Remove the entry in .gitmodules and remove the submodule directory located at path/to/submodule
git rm -f path/to/submodule
```

## 进入之前的Hexo项目目录

```bash
git clone <YOUR-PROJECT-URL> && cd <YOUR-PROJECT>
```

## 托管至master分支中的`docs`目录

https://sspai.com/post/59904中推荐将Github Page使用docs目录进行部署，但是实测目前该方法已经失效。

`Github`对于个人用户的`<username>.github.io`或`<orgname>.github.io`的Page页面现在只能通过`master`分支进行部署，不支持自定义，也[不支持使用`docs`目录](https://docs.github.com/en/enterprise/2.14/user/articles/configuring-a-publishing-source-for-github-pages#publishing-your-github-pages-site-from-a-docs-folder-on-your-master-branch)了。

# 参考来源

https://scarletsky.github.io/2019/05/02/migrate-hexo-to-hugo/

https://www.flysnow.org/2018/07/29/from-hexo-to-hugo.html

https://io-oi.me/tech/hugo-vs-hexo/

https://ouuan.github.io/post/from-hexo-to-hugo/