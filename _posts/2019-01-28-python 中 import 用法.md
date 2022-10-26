---
layout:     post
title:      "python 中 import 用法"
subtitle:    "python 中 import 同级目录、子目录、上级目录的方法及 sys.path.append() 说明"
date:       2019-01-28 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
---



如果是自己遍写的依赖包，又不想安装到 python 的相应目录，可以放到本目录里进行 import 进行调用。



## 不同目录 import 方法

### 同级目录

程序结构如下：

```
-- src
	|-- mod.py
	|-- test.py
```

若在程序 test.py 中导入模块 mod, 则直接使用

```python
import mod
from . import mod
# 或
from mod import *
```



### 子目录

程序结构如下：

```
-- src
	|-- mod1.py
	|-- lib
	|	|-- mod2.py
	|	|-- __init__.py
	|-- test.py
```

这时看到 test.py 和 lib 目录（即 mod2.py 的父级目录），如果想在程序 test.py 中导入模块 mod2.py，可以在 lib 件夹中建立空文件 **\_\_init\_\_.py** 文件(也可以在该文件中自定义输出模块接口)，然后使用：

```python
from lib.mod2 import *
# 或
import lib.mod2
```



### 上级目录

程序结构如下：

```
-- src
	|-- mod1.py
	|-- lib
	|	|-- mod2.py
	|	|-- __init__.py
	|-- sub
	|	|-- test.py
```

这里想要实现 test.py 调用 mod1.py 和 mod2.py，做法是我们先跳到 src 目录下面，直接可以调用 mod1，然后在 lib 下建一个空文件 **\_\_init\_\_.py**，就可以像第二步调用子目录下的模块一样，通过 import  lib.mod2 进行调用了，具体代码如下：

```python
import sys
sys.path.append("..") # 相当于从“当前目录”进入到“上级目录”。
import mod1
import lib.mod2
```

注意：如果我们希望 `import xxx`，则需要 `xxx` 的上级目录在 `sys.path` 中



## sys.path.append() 说明

执行代码：

```python
import sys
print(sys.path)
```

输出如下：

```
['', 'E:\\Anaconda3\\python35.zip', 'E:\\Anaconda3\\DLLs', 'E:\\Anaconda3\\lib', 'E:\\Anaconda3', 'C:\\Users\\77238\\AppData\\Roaming\\Python\\Python35\\site-packages', 'E:\\Anaconda3\\lib\\site-packages', 'E:\\Anaconda3\\lib\\site-packages\\Sphinx-1.4.6-py3.5.egg', 'E:\\Anaconda3\\lib\\site-packages\\win32', 'E:\\Anaconda3\\lib\\site-packages\\win32\\lib', 'E:\\Anaconda3\\lib\\site-packages\\Pythonwin', 'E:\\Anaconda3\\lib\\site-packages\\setuptools-27.2.0-py3.5.egg']
```

执行代码：

```python
sys.path.append("..")
print(sys.path)
```

输出如下：

```
['', 'E:\\Anaconda3\\python35.zip', 'E:\\Anaconda3\\DLLs', 'E:\\Anaconda3\\lib', 'E:\\Anaconda3', 'C:\\Users\\77238\\AppData\\Roaming\\Python\\Python35\\site-packages', 'E:\\Anaconda3\\lib\\site-packages', 'E:\\Anaconda3\\lib\\site-packages\\Sphinx-1.4.6-py3.5.egg', 'E:\\Anaconda3\\lib\\site-packages\\win32', 'E:\\Anaconda3\\lib\\site-packages\\win32\\lib', 'E:\\Anaconda3\\lib\\site-packages\\Pythonwin', 'E:\\Anaconda3\\lib\\site-packages\\setuptools-27.2.0-py3.5.egg', '..']
```

sys.path 返回的是一个列表，该列表已经添加到系统的环境变量的路径。当我们要添加自己的搜索目录路径时，可以通过列表的 `sys.path.append()` 方法添加。其中 `sys.path.append("..")` 为添加上级目录，还有 `sys.path.append("../..")` 为添加上上级目录。





## 参考资料及致谢

[Python中import导入上一级目录模块及循环import问题的解决](https://www.cnblogs.com/sjy18039225956/p/9265461.html)