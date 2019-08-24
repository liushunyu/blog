---
layout:     post
title:      "Github Pages 搭建博客"
subtitle:    "Window 下 Github Pages + Jekyll 搭建博客"
date:       2019-01-10
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - github
    - jekyll
    - 环境配置

---




成功搭建出自己的博客，肯定是要记录一下搭建过程的啦。



本文主要以 [Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io) 和 [Huxpro](https://github.com/Huxpro/huxpro.github.io) 作为参考资料。



## 环境配置

### 参考

- [Jekyll 搭建静态博客](https://gaohaoyang.github.io/2015/02/15/create-my-blog-with-jekyll/)
- [对这个 jekyll 博客主题的改版和重构](https://gaohaoyang.github.io/2016/03/12/jekyll-theme-version-2.0/)



### 流程


1. 下载安装 [Ruby](https://rubyinstaller.org/downloads/)

2. 下载 [RubyGems](https://rubygems.org/pages/download) ，cd 到 RubyGems 文件夹，安装 RubyGems 

   ```bash
   ruby setup.rb
   ```

3. 用 RubyGems 安装 Jekyll

   ```bash
   gem install jekyll
   ```

4. 用 RubyGems 安装插件

    ```bash
    gem install yajl-ruby rouge jekyll-paginate
    ```
    
5. cd 到博客文件夹，开启服务器，watch 为了检测文件夹内的变化，即修改后不需要重新启动 jekyll 

    ```bash
    jekyll serve --watch
    ```

6. 再次启动服务器成功

    ```bash
    jekyll s
    ```

7. 访问 http://localhost:4000/



### 错误

#### jekyll-paginate 依赖缺失

解决方法：

```bash
gem install jekyll-paginate
```



#### 4000端口被占用

原因：

jekyll 启动使用的4000端口被福昕pdf阅读器的自动更新进程占用了，关掉这个进程或改变端口号，jekyll 在本地调试启动服务时就没有问题了。



解决方法：

**关掉这个进程**

输入命令，查看各端口被占用情况

```bash
netstat -ano
```

找到 4000 端口被占用的`PID`，然后在 win10 中进入任务管理器，选择服务选项卡，关闭该`PID`对应的服务就好了。



**指定端口号启动 jekyll 服务**

在启动jekyll服务的时候指定端口号，如下：

```bash
jekyll serve --port 3000
```

这样在浏览器中输入 http://localhost:3000/ 就可以访问了。



还可以在配置文件`_config.yml`中添加端口号设置：

```
# 修改文件如下
# port
port: 1234
```

此时，启动 jekyll 服务后，访问 http://localhost:1234/ 即可。



## 博客搭建

直接 clone 或者 fork [Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io) 或者 [Huxpro](https://github.com/Huxpro/huxpro.github.io) 大神的博客即可。

我个人对一些细节进行了修改，同样也可以 clone 或者 fork 我的博客 [liushunyu](https://github.com/liushunyu/liushunyu.github.io)

具体配置修改参考他们的 README.md 文件即可。



### 细节修改

[网页动态背景——随鼠标变换的动态线条](https://www.cnblogs.com/qq597585136/p/7019755.html)

[加入页码跳转功能](https://github.com/Gaohaoyang/gaohaoyang.github.io/pull/109/commits/31667a8bc2a5cf7b4a005cf4ba44dc8b42d9c564)

[文章中的catalog怎样实现多级菜单](https://github.com/Huxpro/huxpro.github.io/issues/116#)

[如何添加栏目？](https://github.com/Huxpro/huxpro.github.io/issues/237#)



## 参考资料及致谢

该博客模板是从 [Huxpro](https://github.com/Huxpro/huxpro.github.io) fork 的，具体搭建参考了 [Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io) 的教程，感谢这两位作者。