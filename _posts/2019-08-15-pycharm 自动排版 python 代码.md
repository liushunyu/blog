---
layout:     post
title:      "pycharm 自动排版 python 代码"
subtitle:    "使用自带功能或者 autopep8 自动排版 python 代码"
date:       2019-08-15 11:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
    - pycharm
---



pycharm 使用自带功能或者 autopep8 自动排版 python 代码



## 自带功能

windows 使用 `Ctrl+Alt+L` 快捷键，mac 使用 `command+option+L` 快捷键，就可以对整个文件的代码进行排版了。

> pycharm 菜单栏 -> Code -> Reformat Code



## autopep8

### 安装 autopep8

```bash
pip install autopep8
```



### 配置 autopep8


> pycharm 菜单栏 -> PyCharm -> Preferences -> Tools -> Externel Tools -> 点击+加号



```
Name: autopep8
Programs: autopep8
Parameters: --in-place --aggressive --aggressive $FilePath$
Working directory: $ProjectFileDir$
Advanced Options -> Output Filters: $FILE_PATH$\:$LINE$\:$COLUMN$\:.*
```



### 使用 autopep8

> 右键 python 文件 -> Externel Tools -> autopep8



## 参考资料及致谢

[Pycharm使用技巧（1）——Pycharm中如何快速自动排版Python代码](https://blog.csdn.net/leaf_zizi/article/details/84980960)

[PyCharm使用autopep8按PEP8风格自动排版Python代码](https://www.jianshu.com/p/fcd957accd8a)

[学习 python 编写规范 pep8 的问题笔记](https://www.cnblogs.com/hackpig/p/8290117.html)

[Python PEP8 编码规范中文版](https://blog.csdn.net/ratsniper/article/details/78954852)

[PEP 8 -- Style Guide for Python Code](https://legacy.python.org/dev/peps/pep-0008/)