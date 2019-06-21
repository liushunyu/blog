---
layout:     post
title:      "ImportError: Python is not installed as a framework."
subtitle:    "运行 matplotlib 时出现 ImportError: Python is not installed as a framework."
date:       2019-06-21
author:     Shunyu
header-style: text
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - mac
	- python
---



在 mac 环境下，运行机器学习代码时出现错误提示如下：

```
OMP: Error #15: Initializing libiomp5.dylib, but found libiomp5.dylib already initialized.
OMP: Hint: This means that multiple copies of the OpenMP runtime have been linked into the program. That is dangerous, since it can degrade performance or cause incorrect results. The best thing to do is to ensure that only a single OpenMP runtime is linked into the process, e.g. by avoiding static linking of the OpenMP runtime in any library. As an unsafe, unsupported, undocumented workaround you can set the environment variable KMP_DUPLICATE_LIB_OK=TRUE to allow the program to continue to execute, but that may cause crashes or silently produce incorrect results. For more information, please see http://www.intel.com/software/products/support/.
```



#### 解决方法

```
$ conda install nomkl
```



## 参考资料及致谢

[Python Debug\]Kernel Crash While Running Neural Network with Keras|Jupyter Notebook运行Keras服务器宕机原因及解决方法](https://www.cnblogs.com/sherrydatascience/p/10626474.html)