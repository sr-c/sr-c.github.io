---
title: "在Vscode中配置Python环境"
date: 2020-07-21T15:15:47+08:00
tags: [python, VScode]
categories: 
draft: true
---



使用Anaconda配置python环境，在VScode中使用

<!-- more -->

# 安装

anaconda和VScode的安装都很方便，默认安装即可。Windows中安装在`C://Program Files`目录下可能会存在权限问题，不推荐。

# 配置

## .condarc

使用`conda config`命令新建`.condarc`文件。一般的路径如下：

>- macOS: /Users/Username
>- Linux: ~/.condarc
>- Windows: C:\Users\Username

```yaml
ssl_verify: true
channels:
  - conda-forge
  - bioconda
  - defaults
```

文件规范了各个conda源的优先级。还可以设定镜像软件源的地址，但现在官方源的速度也不错，可以不做设置。

## 新建conda环境

`conda`虽然方便，但为了降低BUG几率，最好保障`base`环境干净。因此，一般都在新建的环境中进行配置。

```bash
conda create -n "scipy" pandas biopython
```

新建一个`scipy`环境，安装了`pandas`,`biopython`方便日常调用。

## 在VScode中调用conda环境

参考[官方文档](https://code.visualstudio.com/docs/python/environments)，首先在VScode扩展市场中安装Microsoft官方的`python`[扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

### 选择环境

使用`Ctrl+Shift+P`打开`Phthon: Select Interpreter`选择python解释器。

![python_interpreter](https://code.visualstudio.com/assets/docs/python/environments/interpreters-list.png)

在其中可直接选择conda环境中的python，然后在VScode界面左下角可以看到当前应用的解释器。

![current_interpreter](https://code.visualstudio.com/assets/docs/python/environments/selected-interpreter-status-bar.png)

### 手动设置解释器

在`settings.json`中可使用`python.pythonPath`字段设置Python的路径。使用`Ctrl + ,`打开`settings.json`

```json
"python.pythonPath": "<path-to-your-interpreter>\\python.exe"
```

### 终端自动激活目标环境

在`settings.json`中配置终端自动激活conda环境，添加如下字段

```json
“terminal.integrated.shellArgs.windows”: [“/K”, “C:\\<path-to-conda-installation>\\Scripts\\activate.bat C:\\<path-to-conda-installation> & conda activate <your-env-name>”]
```

推荐在Windows中，VScode默认调用的终端改为`cmd.exe`, 在`settings.json`中添加

```json
“terminal.integrated.shell.windows”:“C:\\Windows\\System32\\cmd.exe”
```



# 参考来源

https://code.visualstudio.com/docs/python/environments

https://medium.com/@udiyosovzon/how-to-activate-conda-environment-in-vs-code-ce599497f20d

https://stackoverflow.com/a/50993392

