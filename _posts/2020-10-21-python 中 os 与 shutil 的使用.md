---
layout:     post
title:      "python 中 os 与 shutil 的使用"
subtitle:   "python 中 os 与 shutil 的使用"
date:       2020-10-21 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - linux
    - python
---



python 中 os 与 shutil 的使用。



## os

```python
import os

os.system()						# 运行 shell 命令

os.linesep						# 当前操作系统的换行符
os.sep								# 当前操作系统的路径分隔符

os.getcwd()						# 获取当前工作目录，非脚本目录

os.mkdir()						# 创建单个目录
os.makedirs()					# 创建多级目录

os.remove()						# 删除文件
os.rmdir()						# 删除目录（文件夹不能有文件）
os.removedirs()				# 递归删除目录树（文件夹不能有文件）

os.rename("old_name", "new_name")		# 重命名文件或目录

os.path.isfile()			# 检验给出的路径是否是一个文件
os.path.isdir()				# 检验给出的路径是否是一个目录
os.path.isabs()				# 判断是否是绝对路径
os.path.exists()			# 检验给出的路径是否真实存在
os.path.split()				# 返回一个路径的目录名和文件名
os.path.splitext()		# 分离文件扩展名
os.path.dirname()			# 获取文件路径名
os.path.basename()		# 获取一个绝对路径下的文件名
os.path.getsize()			# 获取文件大小
```

- [python os.walk() os.listdir() 与 os.path()](https://liushunyu.github.io/2019/08/15/python-os.walk()-os.listdir()-与-os.path()/)



## shutil

```python
import shutil

shutil.copy("old_file", "destination")
# old_file 只能是文件，destination 可以是文件或文件夹
# 若文件夹不存在则报错，若刚好是最后一个目录是不存在的文件夹会将该文件夹名误认为文件名

shutil.copyfile("old_file", "new_file")		# old_file 和 new_file 都只能是文件
shutil.copytree("old_dir", "new_dir")			# old_dir 和 new_dir 都只能是目录，且 new_dir 必须不存在
shutil.move("source", "destination")			# 移动文件或目录
shutil.rmtree("dir")											# 递归删除目录树（文件夹里可以有文件）
```



## 参考资料及致谢

[Python os&&shutil](https://blog.csdn.net/u012164509/article/details/93995887)

[python中的os,shutil模块的定义以及用法](https://www.cnblogs.com/czaiz/p/7693915.html)