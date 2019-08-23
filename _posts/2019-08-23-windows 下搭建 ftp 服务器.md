---
layout:     post
title:      "Windows 下搭建 FTP 服务器"
subtitle:    "win 10 下搭建 FTP 服务器"
date:       2019-08-23
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - windows
---



同时使用两台电脑的时候要互相传小文件用 u 盘太麻烦了，所以在 win 10 下搭建一个 FTP 服务器来共享文件。



## 搭建 FTP 服务器步骤

#### win 10 开启 FTP 功能

> 控制面板 -> 程序 -> 程序和功能 -> 启用或关闭 Windows 功能 -> 启用下图启用的所有功能



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/1.png"/>



#### 允许 FTP 应用通过防火墙

> 控制面板 -> 系统和安全 -> Windows Defender 防火墙 -> 允许应用或功能通过 Windows Defender 防火墙 -> 更改设置 -> 勾选以下功能



1、FTP 服务器

<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/2.png"/>



2、Windows 服务主进程

如果没有这一项请手动添加：点击 “允许其它应用” -> 浏览 -> 添加 “C:Windows\System32\svchost.exe”

<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/3.png"/>



#### 允许指定端口通过防火墙

> 控制面板 -> 系统和安全 -> Windows Defender 防火墙 -> 高级设置 -> 入站规则 -> 新建规则 -> 端口



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/4.png"/>



> 特定本地端口：FTP 一般使用 21 端口，也可以填写其他



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/5.png"/>



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/6.png"/>



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/7.png"/>



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/8.png"/>



点击完成即可。



#### 添加 FTP 站点

> 开始菜单 -> 搜索 "IIS" -> 打开 "Internet Information Services (IIS)管理器" > 右键 "网站" -> 添加 FTP 站点



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/9.png"/>




> FTP 站点名称：填写任意名称
>
> 内容目录-物理路径：填写要分享的文件夹路径




<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/10.png"/>



> IP 地址：填写本机 IPv4 地址
> 
> 端口：默认 21 端口，也可指定其他
> 
> SSL：无




<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/11.png"/>



> 身份验证：按需选择，"匿名" 可不需要账号密码访问，"基本" 需要账号密码访问
>
> 授权-允许访问：所有用户
>
> 权限：按需选择




<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/12.png"/>



点击完成即可。



注：当我们选择 "身份验证" 为 "基本" 时，意味着我们访问该 FTP 时需要输入账号和密码，而这账号和密码是计算机本地用户的账号和密码，可按下面流程创造新的用户并设置密码，或者使用使用你当前的主机名和密码登录即可。



> 右击 "计算机" -> 管理 -> 本地用户和组 -> 用户



<img width="480" src="/img/in-post/2019-08-15-windows 下搭建 ftp 服务器.assets/13.png"/>



#### 访问 FTP 站点

此时在内网的另一台电脑上访问 `ftp://ip:port` 然后输入账号密码即可成功访问。



## 参考资料及致谢

[windows10搭建FTP服务器](https://www.jianshu.com/p/a9dfce3e0af1)

[Windows下搭建FTP服务器](https://www.cnblogs.com/zhangfengfly/p/6879513.html)

[win10FTP设置用户和密码](https://jingyan.baidu.com/article/358570f69c561dce4724fcac.html)

