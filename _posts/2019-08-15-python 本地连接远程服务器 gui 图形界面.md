---
layout:     post
title:      "本地连接远程服务器 GUI 图形界面"
subtitle:    "在本地显示远程服务器上运行得到的图形界面"
date:       2019-08-15 12:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
    - linux
    - mac
    - windows
    - pycharm
---



最近在使用服务器编程时遇到了无法实时显示可视化界面的问题，最多只能做到将图片保存下来，在本地下载之后再打开，下面我们将介绍 windows 和 mac 如何实现连接远程服务器跑 python 代码实时返回可视化界面的操作，当然配置成功后不局限于 python 运行结果的可视化，包括一切 linux GUI 图形界面都能做到，具体原理主要要参考 X 协议，在[这篇博客](http://www.ipaomi.com/2017/11/09/%E8%BF%9C%E7%A8%8B%E6%98%BE%E7%A4%BA%E6%93%8D%E4%BD%9C-%E6%9C%8D%E5%8A%A1%E5%99%A8-gui-%E7%A8%8B%E5%BA%8F%E5%9B%BE%E5%BD%A2%E5%8C%96%E7%95%8C%E9%9D%A2-%E5%9F%BA%E4%BA%8E-x11-forwarding-centos/)有适当提及。



## 服务器端配置

1、启用 X11 Forwarding

```bash
$ sudo vim /etc/ssh/sshd_config
# 修改文件如下
X11Forwarding yes
X11DisplayOffset 10
```



2、重启 sshd 服务

```bash
service ssh restart
```



## mac 端配置

1、下载安装 [XQuartz](https://www.xquartz.org/)



2、启用 X11 Forwarding

```bash
$ sudo vim /private/etc/ssh/ssh_config
# 修改文件如下
ForwardX11 yes
```



3、打开 XQuartz 的终端

```bash
ssh -Y username@ip:port
```



## windows 端配置

1、下载安装 [MobaXterm](https://mobaxterm.mobatek.net/)

2、直接使用 MobaXterm 连接服务器即可，连接成功后有显示 X11-forwarding 标志



## 代码配置

1、使用上面带有 X 协议的终端连接服务器端输入以下命令获得 `DISPLAY` 值。

```bash
$ printenv grep DISPLAY
# 比如我输出的 DISPLAY 如下
localhost:13.0
```



2、配置代码的环境变量

**第一种方法：在 pycharm 中配置**

> pycharm 菜单栏 -> Run -> Edit Configurations -> 选中我们运行的 configuration -> Environment variables



添加一个 DISPLAY 变量 `DISPLAY=localhost:13.0`

> 注意：如果已经有别的 Environment variables，可用 `; ` 进行隔开， `; ` 前后不需要空格。



**第二种方法：在 python 代码中配置**

```python
import os
os.environ['DISPLAY'] = "localhost:13.0"
```



## 参考资料及致谢

[在Mac上使用远程X11应用](http://blog.17study.com.cn/2018/03/09/remote-xwindows/)

[mac如何ssh连接linux(ubuntu) GUI图形界面](https://blog.csdn.net/dobell/article/details/55047811)

[远程显示(操作) 服务器 GUI 程序(图形化界面) (基于 X11 Forwarding + Centos + MobaXterm)](http://www.ipaomi.com/2017/11/09/%E8%BF%9C%E7%A8%8B%E6%98%BE%E7%A4%BA%E6%93%8D%E4%BD%9C-%E6%9C%8D%E5%8A%A1%E5%99%A8-gui-%E7%A8%8B%E5%BA%8F%E5%9B%BE%E5%BD%A2%E5%8C%96%E7%95%8C%E9%9D%A2-%E5%9F%BA%E4%BA%8E-x11-forwarding-centos/)

[PyCharm使用远程Python解释器并用matplotlib绘图的方法](https://www.jianshu.com/p/a92e474dd657)