---
layout:      post
title:       "python 中配置 linux 环境变量"
subtitle:    "python 中配置 linux 环境变量"
date:        2020-03-31 09:00:00
author:      Shunyu
header-img:  img/post-bg-2015.jpg
header-mask: 0.1
catalog:     true
tags:
    - linux
    - python
---



在某些 python 程序中需要单独配置一些环境变量，又不希望通过 pycharm 配置，有时候比较麻烦。



## 配置 linux 环境变量

```python
import os

# 覆盖重写某项环境变量
os.environ['CUDA_VISIBLE_DEVICES'] = '0,1'

# 在某项环境变量之前加内容
# 注意这里顺序不能反，新加的内容要放在最前面才不会被其他路径影响
os.environ["PATH"] = '/home/lsy/cuda/cuda-10.1/bin' + os.pathsep + os.environ["PATH"]
os.environ["LD_LIBRARY_PATH"] = '/home/lsy/cuda/cuda-10.1/lib64' + os.pathsep + os.environ["LD_LIBRARY_PATH"]
```





## 参考资料及致谢

无

