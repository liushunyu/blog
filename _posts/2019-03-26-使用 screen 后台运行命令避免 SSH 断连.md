---
layout:     post
title:      "使用 screen 后台运行命令避免 SSH 断连"
subtitle:    "Linux 服务器使用 screen 让进程在后台可靠运行，防止客户端 SSH 连接关闭导致进程挂断"
date:       2019-03-26
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - linux
    - documentation
---



在使用服务器的过程中，通常会跑一些比较长时间的代码，这个时候希望能将程序运行在服务器后台，防止客户端断开 SSH 时结束程序。

程序终止原因：SSH 下直接运行的命令会被当作 SSH 的子进程，SSH 作为父进程断掉后，子进程也跟着挂了。



## 使用 `screen`

`screen` 提供了终端模拟器，使它能够在一个真实终端下运行多个全屏的伪终端。



建立一个处于断开模式下的会话（并指定其会话名）：

```bash
screen -S [name]
```



列出所有会话：

```bash
screen -ls
```



重新连接指定会话：

```bash
screen -r [name / pid]
```



离开会话并让程序断续运行（会话终端下）：

```bash
快捷键：CTRL + a d (按住 ctrl 不放，分别按 a 和 d)
```



关闭该 session（会话终端下）：

```bash
# 在该 screen 中退出，退到根下。
exit
```



当我们用 `-r` 连接到 `screen` 会话后，我们就可以在这个伪终端里面为所欲为，再也不用担心 HUP 信号会对我们的进程造成影响，也不用给每个命令前都加上 `nohup` 了。



`screen` 其他具体操作可参考 [linux screen 命令 ：离线运行程序](https://www.cnblogs.com/lifegoesonitself/p/3516718.html)。

`SIGHUP` 信号原理可参考 [linux 技巧：使用 screen 管理你的远程会话](https://www.ibm.com/developerworks/cn/linux/l-cn-screen/)。





## 其他说明

还可以 [使用 `nohub` 实现后台运行服务器]() 的功能，还有一些其他方法可参考 [Linux下让进程在后台可靠运行的几种方法（nohup/&）和前后台运行程序切换](https://blog.csdn.net/u011630575/article/details/51037153)。



## 参考资料及致谢

[Linux下让进程在后台可靠运行的几种方法（nohup/&）和前后台运行程序切换](https://blog.csdn.net/u011630575/article/details/51037153)

[linux screen 命令 ：离线运行程序](https://www.cnblogs.com/lifegoesonitself/p/3516718.html)

[linux 技巧：使用 screen 管理你的远程会话](https://www.ibm.com/developerworks/cn/linux/l-cn-screen/)