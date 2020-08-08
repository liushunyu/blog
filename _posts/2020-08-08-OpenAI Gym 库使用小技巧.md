---
layout:     post
title:      "OpenAI Gym 库使用小技巧"
subtitle:   "OpenAI Gym 库使用小技巧"
date:       2020-08-08 10:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - 强化学习
    - python
---



OpenAI Gym 库使用小技巧。



## gym 库渲染显示

1、使用虚拟帧缓冲区打开 notebook

```
xvfb-run -s "-screen 0 1400x900x24" jupyter notebook
```



2、实现在 notebook 中显示 gym 库的渲染显示

```python
import matplotlib.pyplot as plt
%matplotlib inline
from IPython import display

def show_state(env, step=0, info=""):
    plt.figure(1)
    plt.clf()
    plt.imshow(env.render(mode='rgb_array'))
    plt.title("Step: %d %s" % (step, info))
    plt.axis('off')
    
    display.display(plt.gcf())
    display.clear_output(wait=True)
```



## gym 库列出所有环境

https://github.com/openai/gym/blob/master/gym/envs/__init__.py

```python
from gym import envs
envids = [spec.id for spec in envs.registry.all()]
for envid in sorted(envids):
    print(envid)
```



## 参考资料及致谢

[OpenAI Gym](https://github.com/openai/gym)

[How to run OpenAI Gym .render() over a server](https://stackoverflow.com/questions/40195740/how-to-run-openai-gym-render-over-a-server)

