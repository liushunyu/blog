---
layout:     post
title:      "python 中利用 pickle 保存变量"
subtitle:    "利用 pickle 将变量结构及值，以便下次读取使用"
date:       2019-03-26 11:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
---



在编写 python 项目时，有时候希望将模型训练的结果进行保存，以便下次进行使用，在这里利用 pickle 实现。



## 使用 `pickle`

`pickle` 这个包是 python 自带的，不需要另外再去安装。



具体使用如下：

```python
import pickle
# 保存变量
def save_variable(v,filename):
    f = open(filename, 'wb')
    pickle.dump(v, f)
    f.close()
    return filename

# 读取变量
def load_variable(filename):
    f = open(filename, 'rb')
    r = pickle.load(f)
    f.close()
    return r
 
if __name__=='__main__':
    c = [1, 2, 3, 4, 5, 6, 7]
    filename = save_variable(c, 'test.data')
    d = load_variavle(filename)
    print(d==c)
```



需要注意这里的 file 必须要是以二进制的形式进行操作（写入或读取）。



## 参考资料及致谢

[Python中利用pickle保存变量](https://blog.csdn.net/qq_27575895/article/details/81100232)