---
layout:     post
title:      "Dockerfile 基础格式"
subtitle:    "Dockerfile 基础格式"
date:       2019-07-02
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - docker
    - documentation
---



Dockerfile 基础格式。



## Dockerfile

Dockerfile 命令

```
FROM ubuntu:16.04

MAINTAINER shunyu "shunyu.liu@foxmail.com"

COPY . /home/AEgraph
WORKDIR /home/AEgraph

RUN apt-get update \
 && apt-get install -y python3.5 \
 && apt-get install -y python3-pip \
 && apt-get install -y vim \
 && pip3 install -r /home/AEgraph/requirements.txt
```



| 指令    | 含义解释                                                     |
| ------- | ------------------------------------------------------------ |
| FROM    | FROM debian:stretch表示以debian:stretch作为基础镜像进行构建  |
| RUN     | 可以看出RUN后面跟的其实就是一些shell命令，通过&&将这些脚本连接在了一行执行，这么做的原因是为了减少镜像的层数，每多一行RUN都会给镜像增加一层，所以这里选择将所有命令联结在一起执行以减少层数 |
| ARG     | 特地将这个指令放在RUN之后讲解，这个指令可以进行一些宏定义，比如我定义ENV JAVA_HOME=/opt/jdk，之后RUN后面的shell命令中的${JAVA_HOME}都会被/opt/jdk代替 |
| ENV     | 可以看出这个指令的作用是在shell中设置一些环境变量（其实就是export） |
| COPY    | 顾名思义，就是用来来回复制文件的，COPY . /root/workspace/agent表示将当前文件夹（.表示当前文件夹，即Dockerfile所在文件夹）的所以文件拷贝到容器的/root/workspace/agent文件夹中。通过--from参数也可以从前面阶段的镜像中拷贝文件过来，比如--from=builder表示文件来源不是本地文件系统，而是之前的别名为builder的容器 |
| WORKDIR | 在执行`RUN`后面的shell命令前会先`cd`进`WORKDIR`后面的目录    |






## 参考资料及致谢

[Docker与Dockerfile极简入门文档](https://blog.csdn.net/qq_33256688/article/details/80319673)

[Docker Dockerfile 定制镜像](https://blog.csdn.net/wo18237095579/article/details/80540571)