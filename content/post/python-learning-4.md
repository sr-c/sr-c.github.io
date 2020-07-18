---
title: 【Python】持久存储
tags: [Python, Head First]
date: 2018-10-28 11:01:13
permalink:
categories:
description:
---
<p class="description">使用Python存储输出的数据</p>

<!-- more -->

## 用with处理文件

使用try/except处理文件时，若在文件处理过程出现错误，其后的代码都不会运行。此时，应当将关闭文件的语句加入finally语句中。

更简便的方法是，使用with语句保证关闭打开的数据文件。

```python
try:
    data = open('its.txt', 'w')
    print("It's...", file=data)
except IOError as err:
    print('File error: ' + str(err))
finally:
    if 'data' in locals():
        data.close()
```

等价于

```python
try:
    with open('its.txt', 'w') as data:
        print("It's...", file=data)
except IOError as err:
    print("File error: " + str(err))
```



## "腌制"数据

使用标准库pickle保存和加载Python数据对象。

### 使用dump保存，使用load恢复

```python
import pickle

with open('mydata.pickle','wb') as mysavedata:
    pickle.dump([1,2,'three'], mysavedata)
    ...
with open('mydata.pickle','rb') as myrestoredata:
    a_list = pickle.load(myrestoredata)
    
print(a_list)
```

```python
import pickle

try:
    with open('man_data.txt', 'wb') as man_file, open('other_data.txt', 'wb') as other_file:
        pickle.dump(man, man_file)
        pickle.dump(other, other_file)
except IOError as err:
    print('File error: ' + str(err))
except pickle.PickleError as perr:
    print('Pickling error: ' + str(perr))
```

**使用pickle的通用文件I/O才是上策**

## 参考来源

[《Head First Python》](https://www.oreilly.com/library/view/head-first-python/9781491919521/)

<hr />