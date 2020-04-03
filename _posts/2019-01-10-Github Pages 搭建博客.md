---
layout:     post
title:      "Github Pages 搭建博客"
subtitle:    "Github Pages + Jekyll 搭建博客"
date:       2019-01-10 09:00:00
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



## 博客搭建

直接 clone 或者 fork [Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io) 或者 [Huxpro](https://github.com/Huxpro/huxpro.github.io) 大神的博客即可。

我个人对一些细节进行了修改，同样也可以 clone 或者 fork 我的博客 [liushunyu](https://github.com/liushunyu/liushunyu.github.io)

具体配置修改参考他们的 README.md 文件即可。

### 1. 创建 GitHub Pages 项目

新建一个仓库 repository，仓库名字为 `xxx.github.io`，其中 `xxx` 为你 github 的 `username`。

<img width="480" src="/img/in-post/2019-01-10-Github Pages 搭建博客.assets/1.png"/>

然后把自己新建的仓库 clone下来。



### 2. 使用主题模版

首先 clone 你想使用的主题模版

```bash
git clone https://github.com/liushunyu/liushunyu.github.io.git
```

接着把主题模版文件夹中除了 .git 文件夹全部复制到自己仓库所在的文件夹中，然后 `commit` 一下更新 github 上自己的仓库，这时候我们通过网址 `https://xxx.github.io/` 就能访问到博客啦。



### 3. 细节修改

接下来列出一些所需要修改的细节之处，请将这些地方的信息修改为自己的信息。

#### `_config.yml` 文件

Site 设置：一些基本信息设置，需要修改为自己的信息。

```yml
# Site settings
title: Richard's Blog
SEOTitle: Richard 的博客 | Richard's Blog
header-img: img/index-bg.jpg
email: shunyu.liu@foxmail.com
description: "Richard 的博客"
keyword: "Richard 的博客, Richard's Blog"
url: "http://liushunyu.github.io"  # your host, for absolute URL
baseurl: ""                        # for example, '/blog' if your blog hosted on 'host/blog'
```



SNS 设置：修改为自己的社交网络账号，没有社交网络账号的可以注释掉，会显示在侧边栏以及最底下。

```yml
# SNS settings
RSS: false
github_username:    liushunyu
#weibo_username:     liushunyu
#zhihu_username:     liushunyu
#twitter_username:   liushunyu
#facebook_username:  liushunyu
#linkedin_username:  firstname-lastname-idxxxx
```



Gitalk 设置：具体 Gitalk 搭建在下方给出。

```yml
# Gitalk settings
gitalk:
   enable: true
   owner: liushunyu
   repo: liushunyu.github.io
   clientID: 7fa2aa7670ebae7bf2b4
   clientSecret: 436e2734b724a9dc65bba97f11b6cb550c700044
   admin: liushunyu
```



Sidebar 设置：修改 `sidebar-about-description`，头像图片可替换 `/img/header.jpg`。

```yml
# Sidebar settings
sidebar: true                           # whether or not using Sidebar.
sidebar-about-description: "斯丢彼得。"
sidebar-avatar: /img/header.jpg         # use absolute URL, seeing it's used in both `/` and `/about/`
```



Featured Tags 设置：`featured-condition-size` 规定了每个 tag 的文章数量大于多少时才在侧边栏显示，一开始可以先设为 `0`。 

```yml
# Featured Tags
featured-tags: true                     # whether or not using Feature-Tags
featured-condition-size: 1              # A tag will be featured if the size of it is more than this condition value
```



Friends 设置

```yml
# Friends
friends: [
    {
      title: "又又",
      href: "https://github.com/isluoshuang"
    }
]
```



#### `index.html` 文件

修改 `description`

```html
---
layout: page
description: "怕什么真理无穷，<br>进一寸有进一寸的欢喜。"
---
```



#### `page/archive.html` 文件

修改 `description`

```html
---
title: Archive
layout: default
description: 路漫漫其修远兮，吾将上下而求索。
header-img: "img/archive-bg.jpg"
---
```



#### `page/about.html` 文件

修改 `description`

```html
---
layout: page
title: About
description: "你好啊，这里是 Richard。"
header-img: "img/about-bg.jpg"
multilingual: true
---
```



#### `_includes/about` 文件夹

修改 `zh.md` 文件和 `en.md` 文件中的自我介绍部分。



#### `_includes/footer.html` 文件

修改 `Powered by` 后面的 `href` 内容和文本，还需要修改 `iframe` 中的 `src`。

```html
Powered by <a href="https://github.com/liushunyu/liushunyu.github.io">Richard's Blog</a> |
<iframe
        style="margin-left: 2px; margin-bottom:-5px;"
        frameborder="0" scrolling="0" width="100px" height="20px"
        src="https://ghbtns.com/github-btn.html?user=liushunyu&repo=liushunyu.github.io&type=star&count=true" >
</iframe>
```



#### `img` 文件夹

请将这里面的图片替换为你自己喜欢的图片以及头像，注意 `_posts` 文件夹中的文章的图片都保存在 `img/in-post` 中。



#### `pwa` 文件夹

请将 `icon` 里面的图片替换为你自己的头像，还需要修改 `manifest.json` 文件中的 `name`、`short_name` 和 `description`。

```json
"name": "Richard's Blog",
"short_name": "Richard's Blog",
"description": "About an tracker who loves world.",
```



#### `sw.js` 文件

修改 `PRECACHE_LIST`，这里是缓存列表，可以把需要缓存的 `img/` 下的图片写在这。

```js
const PRECACHE_LIST = [
  "./",
  "./offline.html",
  "./js/jquery.min.js",
  "./js/bootstrap.min.js",
  "./js/hux-blog.min.js",
  "./js/snackbar.js",
  "./img/header.jpg",
  "./img/index-bg.jpg",
  "./img/about-bg.jpg",
  "./img/archive-bg.jpg",
  "./img/post-bg-2015.jpg",
  "./img/404-bg.jpg",
  "./css/hux-blog.min.css",
  "./css/bootstrap.min.css"
  // "//cdnjs.cloudflare.com/ajax/libs/font-awesome/4.6.3/css/font-awesome.min.css",
  // "//cdnjs.cloudflare.com/ajax/libs/font-awesome/4.6.3/fonts/fontawesome-webfont.woff2?v=4.6.3",
  // "//cdnjs.cloudflare.com/ajax/libs/fastclick/1.0.6/fastclick.min.js"
]
```

修改 `HOSTNAME_WHITELIST`，将中间的那个域名替换为自己的。

```
const HOSTNAME_WHITELIST = [
  self.location.hostname,
  "liushunyu.github.io",
  "yanshuo.io",
  "cdnjs.cloudflare.com"
]
```



#### `README.md` 文件

修改 `README.md` 文件。



### 4. Gitalk 设置

1. 新建 Github OAuth Application

   > Github 头像下拉菜单 -> Settings -> 左边 Developer settings 下的 OAuth Apps -> New OAuth App -> 填写相关信息

<img width="480" src="/img/in-post/2019-01-10-Github Pages 搭建博客.assets/2.png"/>

2. 填写相关信息

<img width="480" src="/img/in-post/2019-01-10-Github Pages 搭建博客.assets/3.png"/>

3. 获得 `Client ID` 和 `Client Secret`

   点击刚刚新建的 OAuth Application，便可以看到该 App 的 `Client ID` 和 `Client Secret`。

4. Gitalk 设置 

   回到刚刚博客的 `_config.yml` 文件中进行 Gitalk 设置，

```yml
# Gitalk settings
gitalk:
   enable: true
   owner: [Github 用户名]
   repo: [博客仓库名]
   clientID: [App 的 Client ID]
   clientSecret: [App 的 Client Secret]
   admin: [Github 用户名]
```



### 5. 写博客

博客草稿存储在 `_posts/_drafts` 中，这里面的内容不会被上传到 github。

博客正文直接放在 `_posts` 中，其中每篇文章开头要包含一下内容，特别注意时间需要精确到时分秒。

```markdown
---
layout:     post
title:      "主标题"
subtitle:   "副标题"
date:       2019-01-01 09:00:00
author:     作者名
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - 标签1
    - 标签2
---
```



### 6. 其他功能

博客的一些其他功能可以参考 [Huxpro](https://github.com/Huxpro/huxpro.github.io) 大神的 `README.md` 文件进行修改，还有一些仍未实现的功能可以参考下面的链接：

[网页动态背景——随鼠标变换的动态线条](https://www.cnblogs.com/qq597585136/p/7019755.html)

[加入页码跳转功能](https://github.com/Gaohaoyang/gaohaoyang.github.io/pull/109/commits/31667a8bc2a5cf7b4a005cf4ba44dc8b42d9c564)

[文章中的catalog怎样实现多级菜单](https://github.com/Huxpro/huxpro.github.io/issues/116#)

[如何添加栏目？](https://github.com/Huxpro/huxpro.github.io/issues/237#)



## jekyll 环境配置

如果希望自己使用 jekyll 编写代码并编译可以参考以下流程，如果没这需求可以跳过。

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



## 参考资料及致谢

该博客模板是从 [Huxpro](https://github.com/Huxpro/huxpro.github.io) fork 的，具体搭建参考了 [Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io) 的教程，感谢这两位作者。

[Jekyll 搭建静态博客](https://gaohaoyang.github.io/2015/02/15/create-my-blog-with-jekyll/)

[对这个 jekyll 博客主题的改版和重构](https://gaohaoyang.github.io/2016/03/12/jekyll-theme-version-2.0/)

[使用github pages搭建个人博客](https://www.jianshu.com/p/418b5695e6ea)

[Mac 一步一步教你在Jekyll博客添加评论系统](https://yizibi.github.io/2018/09/26/Mac-%E4%B8%80%E6%AD%A5%E4%B8%80%E6%AD%A5%E6%95%99%E4%BD%A0%E5%9C%A8Jekyll%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E8%AF%84%E8%AE%BA%E7%B3%BB%E7%BB%9F/)

[为博客添加 Gitalk 评论插件](https://www.jianshu.com/p/78c64d07124d)

[添加 gitalk 评论模块，在_config.yml 进行配置](https://github.com/Huxpro/huxpro.github.io/pull/238/commits/b5ae8f08fe11d69ab419da65049dd2aece221e4f)

[搜索 gitalk in Alkane0050.Github.io](https://github.com/Alkane0050/Alkane0050.Github.io/search?q=gitalk&unscoped_q=gitalk)

