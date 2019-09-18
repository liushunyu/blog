---
layout:     post
title:      "iterm2 + zsh + vim 配置"
subtitle:    "macOS 下进行 iterm2 + zsh + vim 配置"
date:       2019-05-15
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - mac
---



弄个好看的终端玩。



## 终端配置

下载 [iTerm2](https://www.iterm2.com/downloads.html)



将 bash 终端切换为 zsh 终端

```bash
chsh -s /bin/zsh
```



下载 oh my zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```



下载 powerlevel9k 主题样式

```bash
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```



修改 zsh 主题样式为 powerlevel9k（此时终端显示乱码，需安装 Powerline 字体）

```bash
$ vim ~/.zshrc
# 修改文件如下
ZSH_THEME="powerlevel9k/powerlevel9k"  # 修@更改主题样式
POWERLEVEL9K_CONTEXT_TEMPLATE=“lsy”  # 加@更改命令前面的用户名
```



下载安装 [Powerline 字体](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fpowerline%2Ffonts%2Fblob%2Fmaster%2FMeslo%20Slashed%2FMeslo%20LG%20M%20Regular%20for%20Powerline.ttf)

> Preferences -> Profiles -> Text -> Font -> Change Font -> 选择 “Meslo LG M Regular for Powerline”



修改配色

> Preferences -> Profiles -> Colors -> Color Presets -> Solarized Dark



## Hotkey 设置

Hotkey 设置

> Preferences -> Keys -> Show all windows with a system-wide hotkey
>
> 设置 Hotkey: command + .



Hotkey Window 设置

> Preferences -> Keys -> Create a Dedicated Hotkey Window
>
> 设置 Hotkey: option + .
>
> 勾选 Pin hotkey window
>
> 勾选 Floating window
>
> Preference -> Profiles -> Hotkey Window -> Window -> Space -> All Spaces
>
> Preference -> Profiles -> Hotkey Window -> Window -> Style -> Full-Width Bottom of Screen



## 插件设置

安装 `zsh-autosuggestions` 

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```



修改提示透明度

```bash
$ vim ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
# 修改文件如下
typeset -g ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=10'
```





安装 `zsh-syntax-highlighting` 

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```



添加至 `plugins`

```bash
$ vim ~/.zshrc
# 修改文件如下
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```



## 添加 bash 中的环境路径

```bash
$ vim ~/.zshrc
# 修改文件如下
source ~/.bash_profile # 加 bash 中.bash_profile 全部环境变量加入
```



## vim 设置

下载 [solarized](http://ethanschoonover.com/solarized/files/solarized.zip) 配色

```bash
cd solarized/vim-colors-solarized/colors
mkdir -p ~/.vim/colors 
cp solarized.vim ~/.vim/colors/
```



设置 vim

```bash
$ vim ~/.vimrc
# 修改文件如下
syntax on
filetype on
set number
set ruler
colorscheme solarized
```



## 显示 conda 环境名称

```bash
$ vim ~/.oh-my-zsh/custom/themes/powerlevel9k/powerlevel9k.zsh-theme

# 修改文件如下
# 将
defined POWERLEVEL9K_LEFT_PROMPT_ELEMENTS || POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(context dir vcs)
# 修改为
defined POWERLEVEL9K_LEFT_PROMPT_ELEMENTS || POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(anaconda context dir vcs)
```



## 参考资料及致谢

[打造Mac下最强终端，iTerm2 + Oh My Zsh + powerlevel9k](https://feeeei.com/archives/27/?utm_source=wechat_session&utm_medium=social&utm_oi=639600489608777728)

[Mac OS 终端利器 iTerm2](https://www.cnblogs.com/xishuai/p/mac-iterm2.html)

[如何让iterm2 在任何界面呼入呼出？](https://www.wengbi.com/thread_44003_1.html)

[iterm2 如何禁用自动隐藏?](https://www.zhihu.com/question/35568215)

[ITerm2配置-让你的mac命令行更加丰富高效](https://www.jianshu.com/p/405956cdaca6)

[如何解决zsh + oh-my-zsh 使用conda时不显示环境名称的问题](https://www.cnblogs.com/czy-skyline/p/10941546.html)