---
layout:     post
title:      "cuda + cudnn 用户配置"
subtitle:    "为用户配置单独的 cuda 和 cudnn"
date:       2019-10-31 10:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - linux
    - cuda
    - cudnn
---



为了使用 gpu 跑程序，配置 python 虚拟环境进行隔离外，最好为用户配置单独的 cuda 和 cudnn 进行隔离，同时要注意 [tensorflow 版本和 cuda 的对应关系](https://tensorflow.google.cn/install/source)，[pytorch 版本和 cuda 的对应关系](https://pytorch.org/get-started/locally/)，此外如果是 root 用户第一次安装需要一些依赖等请参考官方教程 [NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/archive/10.1/cuda-installation-guide-linux/index.html#abstract)。



## Cuda 安装

#### 下载 [cuda](https://developer.nvidia.com/cuda-toolkit-archive)

选择所需要的版本，然后选择系统信息，**Installer Type** 选择 **runfile (local)**，接着就会出现下载安装的流程。

```bash
wget -c http://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda_10.1.243_418.87.00_linux.run
# -c 为断电重传
```



#### 安装 cuda

```bash
# 必须先创建文件夹，不然会报错
mkdir ~/cuda
sh cuda_10.1.243_418.87.00_linux.run
```

第一步：如果提示系统已安装了 cuda，直接忽略选择 **Continute**

<img width="480" src="/img/in-post/2019-10-31-cuda + cudnn 用户配置.assets/1.png"/>



第二步：输入 `accept` 同意协议

<img width="480" src="/img/in-post/2019-10-31-cuda + cudnn 用户配置.assets/2.png"/>



第三步：CUDA Installer：取消 **Driver** 的勾选，然后进去 **Options** 进行安装设置

<img width="480" src="/img/in-post/2019-10-31-cuda + cudnn 用户配置.assets/3.png"/>



第四步：需要进行配置的有 **Toolkit Options** 和 **Library install path** 两项

> 留意一下 **Samples Options** 中的 **Install Path** 是不是 `/home/lsy/cuda`

<img width="480" src="/img/in-post/2019-10-31-cuda + cudnn 用户配置.assets/4.png"/>

a. 配置 **Toolkit Options**：

- 取消全部选项的勾选
- Change Toolkit Install Path：`/home/lsy/cuda/cuda-10.1`

<img width="480" src="/img/in-post/2019-10-31-cuda + cudnn 用户配置.assets/5.png"/>



b. 配置 **Library install path**：`/home/lsy/cuda/cuda-10.1`

- 不太清楚这个路径到底是什么比较好，没试过系统默认路径会定位到哪里
- 安装成功后会有这三个目录：`/lib64`、`/include`、`/src` ，而且发现 `cuda-10.1` 下也有这三个目录，内容部分重复
- 经过对比 cuda-10.0 的内容，发现东西都放到了一起，所以个人觉得这里填 `/home/lsy/cuda/cuda-10.1` 可能 OK



#### 安装成功

会发现安装在此路径下：`/home/lsy/cuda/cuda-10.1`



## Cudnn 安装

第一步：下载 [cudnn](https://developer.nvidia.com/rdp/cudnn-archive)，选择与 cuda 版本对应的 cudnn，然后选择下载 **cuDNN Library for Linux**

> 注：由于下载 cuDNN 需要登录帐户，直接在终端用指令下载可能会失败，可以先在windows上注册个帐户下载好再上传到服务器。

第二步：安装 cudnn

```bash
# 解压 cudnn 文件夹，解压出来的文件夹名字为 cuda
# 为了不要和我们的 cuda 文件夹冲突了，最好在别的地方解压
tar -zvxf cudnn-10.1-linux-x64-v7.6.5.32.tgz

# 将 cudnn 解压出来的东西移动到 cuda 中
mv cuda/include/cudnn.h ~/cuda/cuda-10.1/include/
mv cuda/lib64/libcudnn* ~/cuda/cuda-10.1/lib64/

# 移动完有用的东西后剩下的可以删掉
rm -rf cuda

# 赋予读权限
chmod a+r ~/cuda/cuda-10.1/include/cudnn.h ~/cuda/cuda-10.1/lib64/libcudnn*
```

第三步：配置环境变量

```bash
$ vim ~/.profile
# 修改文件如下
export CUDA_HOME="/home/lsy/cuda/cuda-10.1"
export PATH="$CUDA_HOME/bin:$PATH"
export LD_LIBRARY_PATH="$CUDA_HOME/lib64:$LD_LIBRARY_PATH"
```

第四步：激活环境变量

```bash
source ~/.profile
```



## 查看安装情况

```bash
# 查看是否安装成功
nvcc -V 
# 查看 nvcc 位置
which nvcc
```



## Cuda 报错

错误：

> OSError: libcublas.so.10: cannot open shared object file: No such file or directory

解决：

```bash
conda install -c anaconda cudatoolkit=10.1
```



错误：

> NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.

解决：

```bash
# 安装 dkms
sudo apt-get install dkms

# 查看驱动版本号
ll /usr/src/ 

# 安装对应驱动
sudo dkms install -m nvidia -v 450.51.06
```



错误：

> The current PyTorch install supports CUDA capabilities sm_37 sm_50 sm_60 sm_70.
>
> If you want to use the NVIDIA A40 GPU with PyTorch，please check the instructions at https://pytorch.org/get-started/locally/
>
> RuntimeError: CUDA error: no kernel image is available for execution on the device

解决：

```bash
# 查看 torch 对应 cuda 版本，原因是版本相对于 GPU 太落后
import torch
torch.version.cuda

# 安装 dkms
pip install torch==1.8.1+cu111 -f https://download.pytorch.org/whl/torch_stable.html --timeout 6000000
```



## 参考资料及致谢

[NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/archive/10.1/cuda-installation-guide-linux/index.html#abstract)

[非root用户在linux下安装多个版本的CUDA和cuDNN](https://blog.csdn.net/hizengbiao/article/details/88625044)

[非root用户安装CUDA和CuDNN](https://blog.csdn.net/supinyu/article/details/84024982)

