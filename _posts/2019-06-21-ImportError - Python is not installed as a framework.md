---
layout:     post
title:      "ImportError: Python is not installed as a framework."
subtitle:    "运行 matplotlib 时出现 ImportError: Python is not installed as a framework."
date:       2019-06-21
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - mac
    - python
---



在 mac 环境下，运行 matplotlib 时出现错误。



#### 错误

错误提示如下：

```
ImportError: Python is not installed as a framework.
The Mac OS X backend will not be able to function correctly if Python is not installed as a framework. See the Python documentation for more information on installing Python as a framework on Mac OS X. Please either reinstall Python as a framework, or try one of the other backends. If you are using (Ana)Conda please install python.app and replace the use of 'python' with 'pythonw'. See 'Working with Matplotlib on OSX' in the Matplotlib FAQ for more information.
```

这是因为 matplotlibrc 文件中没有内容，或者 backend（后端）为 macosx，要改为其他GUI后端：TkAgg、Wx、QtAgg、Qt4Agg等



#### 解决方法一

较麻烦，每次新建项目均需要修改

```
$ vim /Users/liushunyu/anaconda3/envs/XXXX/lib/pythonX.X/site-packages/matplotlib/mpl-data/matplotlibrc
# 修改文件如下
backend: TkAgg
```

以上也是 `plt.show()` 不显示图的解决办法。



#### 解决方法二

使用命令，但不改变 matplotlibrc 配置文件中的内容，添加以下代码：

```
import matplotlib
matplotlib.rcParams['backend'] = 'TkAgg'
```

注：`import seaborn as sns` 也可能出现文章开头提示的类似错误，解决方法：`import seaborn as sns` 需写在以上两行代码之后。