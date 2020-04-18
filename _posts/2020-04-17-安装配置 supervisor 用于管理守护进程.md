---
layout:     post
title:      "安装配置 supervisor 用于管理守护进程"
subtitle:    "安装配置 supervisor 用于管理守护进程"
date:       2020-04-16 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - linux
    - supervisor

---



Supervisor 是用 Python 开发的一套通用的进程管理程序，能将一个普通的命令行进程变为后台 daemon，并监控进程状态，异常退出时能自动重启。

记录一下安装配置 supervisor 用于管理守护进程的流程，该软件比较好的功能是可以在进程挂掉时重启进程，还可以通过网页端进行管理。

目前看资料好像说系统自带的 systemd 基本可以替代 supervisor，最下面附带了一些参考资料后面有空研究一下。



## 配置流程

### 安装 supervisor

```bash
apt-get install supervisor
```



### 配置 supervisor

要使用 gpu 需要安装 [nvidia-container-runtime](https://github.com/NVIDIA/nvidia-container-runtime/)

修改配置文件如下：

- [unix_http_server]：注释
- [inet_http_server]：填写 port、username、password
- [supervisorctl]：使用 http 的 serverurl，填写 serverurl、username、password

```config
; supervisor config file
  
;[unix_http_server]
;file=/var/run/supervisor.sock   ; (the path to the socket file)
;chmod=0700                      ; sockef file mode (default 0700)

[inet_http_server]         ; inet (TCP) server disabled by default
port=0.0.0.0:8888          ; (ip_address:port specifier, *:port for all iface)
username=your_username     ; (default is no username (open server))
password=your_password     ; (default is no password (open server))

[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
;serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket
serverurl=http://0.0.0.0:8888  ; use an http:// url to specify an inet socket
username=your_username         ; should be same as http_username if set
password=your_password         ; should be same as http_password if set

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

[include]
files = /etc/supervisor/conf.d/*.conf
```



### 配置具体进程

主要到上面配置文件的最后一部分 include 了所有 `/etc/supervisor/conf.d/*.conf`，所以我们在 `/etc/supervisor/conf.d` 目录下创建具体进程的配置文件，如下所示：

1、创建具体进程的配置文件

```bash
vim /etc/supervisor/conf.d/program_name.conf
```

2、修改配置文件

- 启动程序命令用绝对路径（如使用不同 python 虚拟环境）

- 部分程序需要关闭守护进程（nginx、redis 等）

```config
[program:program_name]
command=/root/anaconda3/envs/process_name/bin/gunicorn -c config_gunicorn.py wsgi:app
directory=/root/program_name
startsecs=0
stopwaitsecs=0
autostart=false
autorestart=true
stdout_logfile=/root/program_name/log/supervisor_stdout.log
stderr_logfile=/root/program_name/log/supervisor_stderr.log
```



## 使用命令

supervisor 启动

```bash
# 根据配置文件启动 supervisord
supervisord -c /etc/supervisor/supervisord.conf

# 更新新的配置到 supervisord
supervisorctl update

# 重新启动配置中的所有程序
supervisorctl reload
```



管理具体进程

```bash
# 启动某个进程
# program_name 为 [program:program_name] 里的 program_name
supervisorctl start program_name

# 停止某个进程
supervisorctl stop program_name

# 重启某个进程
supervisorctl restart program_name
```



web 页面访问管理

- 访问上面设置的 `serverurl`





## 参考资料及致谢

[Python 进程管理工具 Supervisor 使用教程](https://www.cnblogs.com/restran/p/4854623.html)

[python supervisor使用](https://www.cnblogs.com/zhaoding/p/6257363.html)

[supervisord安装使用简记](https://www.cnblogs.com/wswang/p/5795766.html)

[Supervisor使用详解](https://www.jianshu.com/p/0b9054b33db3)

[Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)