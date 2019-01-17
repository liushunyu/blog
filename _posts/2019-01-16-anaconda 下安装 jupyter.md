---
layout:     post
title:      "anaconda 下安装 jupyter notebook"
subtitle:    "jupyter notebook 报错找不到 kernel 的解决及其他理解"
date:       2019-01-16
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - anaconda
    - jupyter notebook
    - 环境配置
---



之前一直用不了 jupyter notebook 很愁，今晚终于搭建成功了，赶紧记录一下，并且还有一些对 anaconda 的理解及运用。



## 环境配置

### anaconda 和 python 的不同

如果你安装了 [anaconda](https://www.anaconda.com/download/) 就不需要安装 python 了，不同版本的 anaconda 包括了对应版本的 python 在内。可以认为 anaconda 是一个 python 的管理系统，里面已经包含了 python，而且还可以使用`conda`命令下载 python 库。

```
anaconda --version
conda --version
conda list                            #查看已安装Python库
conda uninstall Package_name          #卸载已安装Python库
conda install Package_name            #安装Python库
```



### conda批量导出、安装组件(requirements.txt)

conda 批量导出包含环境中所有组件的 requirements.txt 文件

```
conda list -e > requirements.txt
```

conda 批量安装 requirements.txt 文件中包含的组件依赖

```
conda install --yes --file requirements.txt
```



### jupyter notebook

anaconda 中同样也安装了 jupyter notebook，所以不需要再安装了，通过命令即可运行：

```
jupyter notebook
```

但是我出现了以下问题：

```
I couldn't find a kernel matching IPython (Python 2.7). Please select a kernel
```



#### 解决方法

原来是没有安装 ipykernel 库所导致的，安装即可。

```
conda install ipykernel
```



#### 探索过程

##### 查看安装的 kernel

1. 首先打开 Anaconda Prompt 应用。

2. 输入以下命令查看安装的 kernel 和位置。

   ```
   jupyter kernelspec list
   ```

3. 进入安装目录，搜索打开 kernel.json 可查看python的编辑器的路径文件是否与安装路径一样，但是我发现我在这里搜不到这个文件，那就代表我连 kernel 都没有安装，虽然我的 anaconda 自带 python3.5，但这时候我就以为需要安装一个 python3.5 虚拟环境才能得到 python3.5 的 kernel。

   

##### 安装 python 虚拟环境

可通过以下命令查看已有环境：

```
conda info -e
```

在输入这条命令后，我只出现了 `root` 环境，这时我以为自己没有 python3.5 的环境便打算安装。



安装 python 3.5 虚拟环境步骤如下：

```
conda create -n py35 python=3.5
```

但是在这时候我看到 [在jupyter notebook上使用python虚拟环境](https://www.jianshu.com/p/f70ea020e6f9) 博客里有直接在创建环境时便为其预装 ipykernel 的操作：

```
conda create -n py35 python=3.5 ipykernel
```



这个时候我就明白了，jupyter notebook 的 kernel 需要在环境中安装 ipykernel 这个库才可以有，这个时候自然就不需要 python 3.5 虚拟环境，因为我的 `root` 环境便是 python 3.5 的，这个时候只需要在 `root` 环境下安装 `ipykernel` 库即可。



## 致谢

[pip和conda批量导出、安装组件(requirements.txt)](https://blog.csdn.net/chekongfu/article/details/83187591)

[Anaconda jupyter notebook 出现 kernel error 解决办法](https://www.cnblogs.com/wangliman/p/9855352.html)

[用conda创建python虚拟环境](https://blog.csdn.net/lyy14011305/article/details/59500819)

[在jupyter notebook上使用python虚拟环境](https://www.jianshu.com/p/f70ea020e6f9)