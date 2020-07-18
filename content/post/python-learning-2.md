---
title: 【Python】函数模块
tags: [Python, PyPI, Head First]
date: 2018-10-26 12:46:43
permalink:
categories:
description:
---
<p class="description">Python模块与PyPI</p>

<!-- more -->

## 命名空间

> 主Python程序中的代码与一个名为\_\_main\_\_的命名空间关联。将代码放在其单独的模块中时，Python会自动创建一个与模块同名的命名空间。

如果在程序开头通过import导入了某些模块，这些模块中的函数都会属于其特定的命名空间，而不是主程序默认的命名空间\_\_main\_\_，所以在调用时需要特殊指定。

## 使用可选参数

将一个函数的必要参数改为可选参数，需要为这个参数提供一个缺省值。同时，为要给参数提供缺省值，也使得这个参数成为了可选参数。

```python
#!/usr/bin/env python
'''This is the model 'nester.py', server a fuction print_lol() to print a list,which would have lists in it.'''
def print_lol(the_list, indent=False, level=0):
    '''This function take one positional argument called 'the_list', 
    which is any Python list(possibly nested lists).'''
    for each_item in the_list:
        if isinstance(each_item,list):
            print_lol(each_item, level+1)
        else:
            if indent:
            	for tab_stop in range(level):
                	print('\t', end='')
            print(each_item)
            
```

## 构建发布

向函数文本中补充了注释之后，将该模块保存。准备发布(distribution)，其实是一个文件集合，这些文件联合起来使得用户能够构建、打包和发布自己的模块。

1. 为模块创建一个文件夹

   文件夹中放置模块源文件，

2. 在文件夹中创建一个名为`setup.py`的文件

   setup.py文件中包含了此发布的元数据，形式如下：

   ```python
   from distutils.core import setup
   
   setup(
   	name = 'nester',
   	version = '1.0.0',
   	py_modules = ['nester'],
   	author = 'hfpython',
   	author_email = '',
   	url = '',
   	description = '',
   	)
   ```

3. 构建一个发布文件

   ```python
   python setup.py sdist
   ```

4. 将发布安装到自己的Python本地副本中

   ```python
   sudo python setup.py install
   ```

   模块安装后，会放置在特定的路径中，因此，解释器就能够找到该模块。用户可以使用`import sys; sys.path`来查看当前解释器的模块列表。

   ```python
   >>> import sys; sys.path
   ['C:\\Users\\YourUserName\\PycharmProjects\\hfpython', 'C:\\Program Files\\Python37\\Lib\\idlelib', 'C:\\Program Files\\Python37\\python37.zip', 'C:\\Program Files\\Python37\\DLLs', 'C:\\Program Files\\Python37\\lib', 'C:\\Program Files\\Python37', 'C:\\Program Files\\Python37\\lib\\site-packages']
   ```

## 向PyPI上传代码

### 注册PyPI

https://pypi.org/

### 准备上传

```python
# 注册信息，在其中使用之前已注册的信息登陆
python setup.py register
python setup.py sdist upload
```

## 更新模块

在向模块添加了新的API(Application Programming Interface, API)后，需要更新该模块。更新了模块文件后，还需要修改`setup.py`中的`version`，重新本地安装或提交

```python
sudo python setup.py install
python setup.py sdist upload
```

## 参考来源

[《Head First Python》](https://www.oreilly.com/library/view/head-first-python/9781491919521/)

<hr />