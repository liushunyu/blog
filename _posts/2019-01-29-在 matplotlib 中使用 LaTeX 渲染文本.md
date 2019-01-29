---
layout:     post
title:      "在 matplotlib 中使用 LaTeX 渲染文本"
subtitle:    "Windows 中配置适用于 matplotlib 的 CTeX 套装"
date:       2019-01-29
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - LaTeX
    - python
---



使用 CTeX 套装，需要使用一些没有预装的宏包，这时就需要自己安装package了，下面使用自己下载的 type1cm 宏包进行讲解。



最近使用 python 中的 matplotlib 遇到了需要安装 LaTeX 来进行公式文本渲染的问题，由于本人的运行环境是 windows 10，使用了多个 LaTeX 发行版本进行尝试，其中 TeX Live、MikTeX 一直都是运行代码编译失败，后来安装了 CTeX 成功。



## 环境需要

参考 matplotlib 官方文档 [Text rendering With LaTeX](https://matplotlib.org/tutorials/text/usetex.html)，其中提到一共需要三个东西：

- [LaTeX](http://www.tug.org/)
- [dvipng](http://www.nongnu.org/dvipng/) (which may be included with your LaTeX installation)
- [Ghostscript](https://ghostscript.com/) (GPL Ghostscript 9.0 or later is required)

然后这三个东西需要在电脑的环境变量中设置。

最后可以尝试运行官方文档 [Text rendering With LaTeX](https://matplotlib.org/tutorials/text/usetex.html) 中的 demo 代码，运行成功即可。



## 安装过程

#### CTeX 安装

进入官网下载 [CTeX](http://www.ctex.org/HomePage) 进行安装即可，在安装过程中可勾选是否安装 Ghostscript，记得勾选。

尝试了多个 LaTeX 发行版本，发现 CTeX 是在 Windows 下比较符合自己需求的 发行版本了，应该是内置了 dvipng 和 Ghostscript，所以无需再对 dvipng 和 Ghostscript 进行环境配置。



#### type1cm 宏包缺失

在安装好 CTeX 之后进行代码编译时，出现了 type1cm 宏包缺失的问题，发现自己的 CTeX 无法进行自动安装宏包操作，出现 `get host by name failed in tcp_connect()` 的错误，暂时不知道怎么解决，所以采用了手动安装宏包的方法安装了 type1cm 宏包，具体参考 [LaTeX 手动安装宏包](https://liushunyu.github.io/2019/01/29/LaTeX-%E6%89%8B%E5%8A%A8%E5%AE%89%E8%A3%85%E5%AE%8F%E5%8C%85/)。



## 致谢

[Text rendering With LaTeX](https://matplotlib.org/tutorials/text/usetex.html)

[LaTeX手动安装宏包（package）以及生成帮助文档的整套流程](https://www.cnblogs.com/csucat/p/5142459.html)