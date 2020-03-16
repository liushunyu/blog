---
layout:     post
title:      "服务器配置 jupyter notebook"
subtitle:    "linux 服务器配置 jupyter notebook"
date:       2020-01-14
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - linux
    - notebook
---



希望实现在本地访问服务器上的 jupyter notebook。


$$
\sum_a^T\alpha=1+1
$$
aaaa



## 配置远程访问

### 生成 notebook 配置文件

默认情况下，配置文件 `~/.jupyter/jupyter_notebook_config.py` 并不存在，需要自行创建。使用下列命令生成配置文件：

```bash
jupyter notebook --generate-config
```



### 生成密码

#### 自动生成

从 jupyter notebook 5.0 版本开始，提供了一个命令来设置密码：`jupyter notebook password`，生成的密码存储在 `jupyter_notebook_config.json`。

```bash
$ jupyter notebook password
Enter password: ****
Verify password: ****
[NotebookPasswordApp] Wrote hashed password to ~/.jupyter/jupyter_notebook_config.json
```



#### 手动生成

除了使用提供的命令，也可以通过手动安装，首先进入 `python` 编译环境

```bash
python
```

然后输入以下代码

```python
from notebook.auth import passwd
passwd()
```

接着便会提示你输入密码

```
Enter password:lsy
Verify password:lsy
'sha1:41ab85c91b83:4041d6b567240551be10a3b0a1c6a285cc2891f4'
```

这一串 sha1 密文需要手动添加到 `jupyter_notebook_config.py` 中。



### 修改配置文件

``` bash
vim ~/.jupyter/jupyter_notebook_config.py
```

在 `jupyter_notebook_config.py` 中找到下面的行，取消注释并修改。

```python
c.NotebookApp.ip = '10.214.211.205'  # 服务器的 ip 地址
c.NotebookApp.password = u'sha1:41ab85c91b83:4041d6b567240551be10a3b0a1c6a285cc2891f4'  # 手动生存密码的 sha1 密文需要写在这，自动生成的不需要填写
c.NotebookApp.open_browser = False  # 不需要在服务器上打开浏览器
c.NotebookApp.port = 8024  # 可自行指定一个端口, 访问时使用该端口
```



## 配置多个 kernel

首先进入到想要添加到 notebook 的 conda 环境中

```bash
conda activate rl
```

如果没有 `ipykernel`，需要安装 `ipykernel`

```
pip install ipykernel
```

接着便可以将当前的 conda 环境添加到 notebook 的 kernel 中

```bash
python -m ipykernel install --user --name [env name] --display-name "[name in notebook]"
# [env name]: conda 环境名
# [name in notebook]: 在 notebook 中显示的环境名
# 例如：python -m ipykernel install --user --name rl --display-name "rl"
```



## 参考资料及致谢

[设置 jupyter notebook 可远程访问](sdn.net/simple_the_best/article/details/77005400)

[jupyter notebook 支持多conda环境](https://blog.csdn.net/u011622208/article/details/90379584)

