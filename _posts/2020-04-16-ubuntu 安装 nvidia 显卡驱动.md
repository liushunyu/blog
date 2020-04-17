---
layout:     post
title:      "ubuntu 安装 nvidia 显卡驱动"
subtitle:    "ubuntu 安装 nvidia 显卡驱动"
date:       2020-04-16 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
    - nvidia
---



记录一下 ubuntu 安装 nvidia 显卡驱动流程 。



## 查看信息

查看 GPU 型号

```
lspci | grep -i nvidia
```



查看你可以使用的驱动

```
ubuntu-drivers devices
```



## 卸载流程

卸载 nvidia 显卡驱动

```
sudo apt-get remove --purge nvidia-*
sudo apt-get autoremove
sudo apt-get install -f
sudo reboot
```



## 安装流程

### 自动安装

1、添加 ppa 源（这样才会出现最新版的 nvidia 显卡驱动）

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
```

未添加 ppa 源前：

```bash
$ ubuntu-drivers devices
== /sys/devices/pci0000:d8/0000:d8:00.0/0000:d9:00.0 ==
modalias : pci:v000010DEd00001B06sv00001028sd00003600bc03sc00i00
vendor   : NVIDIA Corporation
model    : GP102 [GeForce GTX 1080 Ti]
driver   : nvidia-driver-430 - distro non-free
driver   : nvidia-driver-390 - distro non-free
driver   : nvidia-driver-435 - distro non-free recommended
driver   : xserver-xorg-video-nouveau - distro free builtin
```

添加 ppa 源后：

```bash
$ ubuntu-drivers devices
== /sys/devices/pci0000:d8/0000:d8:00.0/0000:d9:00.0 ==
modalias : pci:v000010DEd00001B06sv00001028sd00003600bc03sc00i00
vendor   : NVIDIA Corporation
model    : GP102 [GeForce GTX 1080 Ti]
driver   : nvidia-driver-440 - third-party free recommended
driver   : nvidia-driver-435 - distro non-free
driver   : nvidia-driver-415 - third-party free
driver   : nvidia-driver-390 - third-party free
driver   : nvidia-driver-410 - third-party free
driver   : xserver-xorg-video-nouveau - distro free builtin
```



2、安装 nvidia 显卡驱动

```bash
# 自动安装 recommended 版本
sudo ubuntu-drivers autoinstall

# 安装指定版本
sudo apt-get install nvidia-435
```



### 手动安装

1、查看 GPU 型号

```
lspci | grep -i nvidia
```



2、到官网[下载NVIDIA官方显卡驱动](https://www.nvidia.com/Download/index.aspx)，然后存储到相应路径并执行安装文件。



### 安装完成

3、重启电脑

```bash
sudo reboot
```



4、查看是否安装成功

```bash
nvidia-smi
```





## 参考资料及致谢

[简单一步在ubuntu18.04下nvidia driver的安装](https://www.jianshu.com/p/4366ed27add9)

[Ubuntu 18.04安装NVIDIA显卡驱动教程](https://www.linuxidc.com/Linux/2019-02/157170.htm)

[Linux(CentOS)下安装NVIDIA GPU驱动](https://www.cnblogs.com/YSPXIZHEN/p/11466145.html)

