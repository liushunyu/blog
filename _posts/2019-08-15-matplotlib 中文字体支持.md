---
layout:     post
title:      "matplotlib 中文字体支持"
subtitle:    "linux 下 matplotlib 中文字体支持"
date:       2019-08-15
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - linux
    - python
    - matplotlib
---



linux 下 matplotlib 中文字体支持设置。



## 中文字体配置

### 下载 ttf 字体文件

下载 ttf 字体文件（如[黑体字体simhei.ttf](https://link.zhihu.com/?target=http%3A//www.font5.com.cn/font_download.php%3Fid%3D151%26part%3D1237887120)）放到 `~/anaconda3/envs/metro/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/`



### 清除缓存

注意一点，要删除缓存文件：

```bash
rm -r ~/.cache/matplotlib
```



### 代码编写

```python
plt.rcParams['font.sans-serif'] = ['SimHei']  # 设置加载的字体名
plt.rcParams['axes.unicode_minus'] = False  # 解决保存图像中负号 '-' 显示为方块的问题
```



## 参考资料及致谢

[Mac下python3.0使用matplotlib中文乱码(方块)](https://blog.csdn.net/qq_24326765/article/details/82467154)

[python2．和ｐｙｔｈｏｎ３．ｘ-matplotlib中文显示为方块-中文不显示-故障原理研究与解决](https://blog.csdn.net/appleyuchi/article/details/82958813)