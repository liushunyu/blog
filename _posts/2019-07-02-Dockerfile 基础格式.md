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





| 指令      | 含义解释                                                     |
| --------- | ------------------------------------------------------------ |
| `FROM`    | `FROM ubuntu:16.04` 表示以 `ubuntu:16.04` 作为基础镜像进行构建。 |
| `RUN`     | 可以看出 `RUN` 后面跟的其实就是一些 `shell` 命令，通过 `&&` 将这些脚本连接在了一行执行，这么做的原因是为了减少镜像的层数，每多一行 `RUN` 都会给镜像增加一层，所以这里选择将所有命令联结在一起执行以减少层数。 |
| `ENV`     | 设置环境变量 `ENV <key>=<value>`，无论是后面的其它指令，如 `RUN`，还是运行时的应用，都可以直接使用这里定义的环境变量 `${key}`。 |
| `ARG`     | 设置环境变量`ARG <key>=<value>`，与 `ENV` 功能不同的是 ` ARG` 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的。环境变量的默认值可以在构建命令 `docker build` 中用 `--build-arg <参数名>=<值>` 来覆盖。 |
| `COPY`    | 用来复制文件，`COPY . /home/AEgraph` 表示将当前文件夹（`.` 表示当前文件夹，即 `Dockerfile` 所在文件夹）的所有文件拷贝到容器的 `/home/AEgraph` 文件夹中。 |
| `WORKDIR` | 在执行`RUN`后面的 `shell` 命令前会先`cd`进`WORKDIR`后面的目录。 |




## 参考资料及致谢

[Docker与Dockerfile极简入门文档](https://blog.csdn.net/qq_33256688/article/details/80319673)

[Docker Dockerfile 定制镜像](https://blog.csdn.net/wo18237095579/article/details/80540571)

[Docker — 从入门到实践](https://docker_practice.gitee.io/)

