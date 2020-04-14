---
layout:     post
title:      "python setup.py 和 pip install . 区别"
subtitle:    "本地包安装中 python setup.py 和 pip install . 区别"
date:       2020-04-13 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
    - pip
---



有时候我们需要安装本地的包到环境中，这里记录 `python setup.py` 和 `pip install .` 区别。



## 主要区别

 `setup.py` 和 `pip` 两种方法都是可以安装本地的包到环境中，其主要区别如下：



### 可编辑性 Editable

|        pip         |         setup.py          | Editable |
| :----------------: | :-----------------------: | :------: |
| `pip install -e .` | `python setup.py develop` |   yes    |
|  `pip install .`   | `python setup.py install` |    no    |

- 不需要可编辑性 Editable：主要是安装典型第三方包，这种包比较稳定，不再需要你去编辑、修改或是调试。
- 需要可编辑性 Editable：主要是安装自己写的包，当你安装一个包后，这个包需要不断编辑修改。



### pip 特点

一般建议使用 pip 安装，因为其有以下特点：

- 可以自动安装依赖项，而 `python setup.py` 则不会自动安装依赖项。
- 可以进行包管理，提供简单的卸载方式。



## 参考资料及致谢

[Setup in virtualenv: `pip install -e .` vs `python setup.py install`](https://stackoverflow.com/questions/45426794/setup-in-virtualenv-pip-install-e-vs-python-setup-py-install)

[在virtualenv中设置：`pip install -e .` vs`python setup.py install`](https://www.jb51.cc/python/241778.html)

[python setup.py install 和python setup.py develop的区别](https://blog.csdn.net/yywan1314520/article/details/50457835)