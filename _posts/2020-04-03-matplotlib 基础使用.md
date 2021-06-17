---
layout:     post
title:      "Matplotlib 基础使用"
subtitle:   "Matplotlib 基础使用"
date:       2020-04-03 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - python
    - matplotlib
    - documentation
---



下面介绍关于 Matplotlib 的基本函数使用。



**请注意使用 `plt.show()`**



使用 jupyter 时需要 `%matplotlib inline`，以便直接在 python console 里面生成图像。

```
import matplotlib.pyplot as plt
%matplotlib inline
```



## scatter 散点图

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成 x, y 的数据
n = 1024
X = np.random.normal(0, 1, n)
Y = np.random.normal(0, 1, n)

plt.figure()
plt.scatter(X, Y, marker='o', color='r', alpha=0.5, edgecolors='k', linewidths=1)
plt.show()
```



## plot 折线图

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成 x, y 的数据
n = 16
X = np.arange(n)
Y = 2 * X + 5

plt.figure()
plt.plot(X, Y, color='r', alpha=0.5, linestyle='-.', linewidth=1, marker='o', markeredgecolor='k', markeredgewidth=1, markerfacecolor='r', markersize=10)
plt.show()
```



## bar 柱状图

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成 x, y 的数据
n = 8
X = np.arange(n)
Y = np.random.uniform(0.0, 1.0, n)

plt.figure()
plt.bar(X, Y, width=0.8, bottom=2, color='r', alpha=0.8, edgecolor='k', linewidth=1)
plt.show()
```



## 等高线图

```python
import matplotlib.pyplot as plt
import numpy as np

# 计算 x, y 坐标对应的高度值
def f(x,y):
    return (1 - x/2 + x**5 + y**3) * np.exp(-x**2,-y**2)

# 生成 x, y 的数据
n = 256
x = np.linspace(-4, 4, n)
y = np.linspace(-4, 4, n)

# 用 x, y 数据生成 mesh 网格状的数据
# 因为等高线的显示是在网格的基础上添加上高度值
X, Y = np.meshgrid(x, y)

plt.figure()

# 绘制填充等高线
plt.contourf(X, Y, f(X,Y), levels=20, alpha=0.5, cmap=plt.cm.hot)

# 绘制添加等高线
C = plt.contour(X, Y, f(X,Y), levels=20, colors='k', alpha=0.5, linewidth=0.5)
plt.clabel(C, inline=True, fontsize=10)

plt.show()
```



## 3D数据

```python
import matplotlib.pyplot as plt
import numpy as np
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure()
ax = Axes3D(fig)

n = 32
x = np.linspace(-4, 4, n)
y = np.linspace(-4, 4, n)
X, Y = np.meshgrid(x, y)
Z = np.sin(np.sqrt(X ** 2 + Y ** 2))

# 绘制3D数据
ax.plot_surface(X, Y, Z, rstride=1, cstride=1, cmap=plt.cm.rainbow)

# 绘制填充等高线
ax.contourf(X, Y, Z, zdir='z', offset=-2, cmap=plt.cm.rainbow)

plt.show()
```



## 绘图技巧

### 定制图像大小

```python
fig = plt.figure(figsize=(12, 7))
fig = plt.figure(figsize=(8.5, 7))
fig = plt.figure(figsize=(5, 7))

ax1 = plt.subplot(2, 1, 1)
ax2 = plt.subplot(12, 1, 8)
ax3 = plt.subplot(3, 1, 3)
```



### 绘制多幅图像

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成 x, y 的数据
n = 8
X = np.arange(n)
Y = 0.1 * X + 0.5

plt.figure()

for i in range(2):
    for j in range(2):
        # subplot 三个参数分别为：行数、列数和索引值（索引从1开始）
        plt.subplot(2, 2, 2 * i + j + 1)
        
        # 绘制子图
        plt.plot(X, Y)
        
        # 子图标题
        plt.title('title')

# 调整子图间距
plt.subplots_adjust(left=None, top=None, right=None, bottom=None, wspace=0.5, hspace=0.5)

# 图标题
plt.suptitle('suptitle')

plt.show()
```



### 绘制图片

```python
plt.imshow(img, cmap=plt.cm.gray_r)
```



### 绘制坐标轴

显示坐标轴（自定义坐标轴刻度）

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成 x, y 的数据
n = 1024
X = np.random.normal(0, 5, n)
Y = np.random.normal(0, 5, n)

plt.figure()
plt.scatter(X, Y, marker='o', color='r', alpha=0.5, edgecolors='k', linewidths=1)

# 坐标轴标签
plt.xlabel("x label", fontsize=20)
plt.ylabel("y label", fontsize=20, labelpad=10)

# 坐标轴字体大小
plt.xticks(fontsize=20)
plt.yticks(fontsize=20)

# 自定义坐标轴刻度
plt.xticks(np.linspace(-1, 1, 6))
plt.yticks([1, 2, 3, 8], ["a", "b", "c", "d"])

# 不显示坐标轴刻度
# plt.xticks([])
# plt.yticks([])

# 坐标轴范围（放在坐标轴刻度下方）
plt.xlim(-1, 2)
plt.ylim(-2, 5)

# 绘制网格（只显示有坐标轴刻度的网格线）
plt.grid(color='r', linestyle='-.', linewidth=2)

plt.show()
```



不显示坐标轴

``` python
import matplotlib.pyplot as plt
import numpy as np

# 生成 x, y 的数据
n = 1024
X = np.random.normal(0, 5, n)
Y = np.random.normal(0, 5, n)

plt.figure()
plt.scatter(X, Y, marker='o', color='r', alpha=0.5, edgecolors='k', linewidths=1)

# 不显示坐标轴
plt.axis('off')

plt.show()
```



多个子图对齐 y 坐标轴

```python
fig.align_ylabels()
```



坐标轴科学计数法

```python
def changex(x, position):
    return int(x/1000)

plt.gca().xaxis.set_major_formatter(FuncFormatter(changex))
```



### 坐标轴截断

```python
ax1 = plt.subplot(12, 1, 8)
plt.setp(ax1.get_xticklines(), visible=False)
ax1.set_yticks([100])
plt.yticks(fontsize=20)
ax1.legend_.remove()

ax2 = plt.subplot(3, 1, 3)
plt.setp(ax2.get_xticklines(), visible=False)
plt.subplots_adjust(hspace=0.1)

ax1.set_ylim((95, 105))
ax2.set_ylim((-2, 40))
ax1.spines['bottom'].set_visible(False)
ax2.spines['top'].set_visible(False)

plt.xticks(fontsize=20)
plt.yticks(fontsize=20)

fig.align_ylabels()
```



## 保存图片

- 需要放在 `plt.show()` 函数之前

```python
plt.savefig('result.pdf', format='pdf', bbox_inches='tight')
plt.savefig('result.png', format='png', bbox_inches='tight')
```



## 属性

#### color 属性

| 字符  |  颜色  |
| :---: | :----: |
| `'b'` |  蓝色  |
| `'g'` |  绿色  |
| `'r'` |  红色  |
| `'c'` |  青色  |
| `'m'` | 品红色 |
| `'y'` |  黄色  |
| `'k'` |  黑色  |
| `'w'` |  白色  |

```python
import matplotlib.colors as colors
color=list(colors.cnames)[i]  # i 为颜色列表下标
```



#### linestyle 属性

|  字符  |    描述    |
| :----: | :--------: |
| `None` |     无     |
| `'-'`  |  实线样式  |
| `'--'` | 短横线样式 |
| `'-.'` | 点划线样式 |
| `':'`  |  虚线样式  |



#### marker 属性

|  字符  |     描述     |
| :----: | :----------: |
| `None` |      无      |
| `'.'`  |    点标记    |
| `','`  |   像素标记   |
| `'o'`  |    圆标记    |
| `'v'`  |  倒三角标记  |
| `'^'`  |  正三角标记  |
| `'<'`  |  左三角标记  |
| `'>'`  |  右三角标记  |
| `'1'`  |  下箭头标记  |
| `'2'`  |  上箭头标记  |
| `'3'`  |  左箭头标记  |
| `'4'`  |  右箭头标记  |
| `'s'`  |  正方形标记  |
| `'p'`  |  五边形标记  |
| `'*'`  |   星形标记   |
| `'h'`  | 六边形标记 1 |
| `'H'`  | 六边形标记 2 |
| `'+'`  |   加号标记   |
| `'x'`  |    X 标记    |
| `'D'`  |   菱形标记   |
| `'d'`  |  窄菱形标记  |
| `'|'`  |  竖直线标记  |
| `'_'`  |  水平线标记  |



### cmap 属性

|       字符       |  描述  |
| :--------------: | :----: |
| `plt.cm.gray_r`  | 灰度图 |
|   `plt.cm.hot`   | 热力图 |
| `plt.cm.rainbow` | 彩虹图 |



## 强化学习 gym 库渲染显示

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



## Legend 使用

```python
plt.legend(loc=loc, ncol=ncol)

loc = ['upper left' , 'upper center', 'upper right', 
       'center left', 'center', 'center right', 
       'lower left', 'lower center', 'lower right']

fig = plt.gca()
fig.legend_.remove()
```





## 参考资料及致谢

[Matplotlib随记2](https://www.cnblogs.com/huanggen/p/7533088.html)

[NumPy Matplotlib](https://www.runoob.com/numpy/numpy-matplotlib.html)

[How to run OpenAI Gym .render() over a server](https://stackoverflow.com/questions/40195740/how-to-run-openai-gym-render-over-a-server)

[Python-Matplotlib用户必备的画图速查表](https://zhuanlan.zhihu.com/p/197854613)

[matplotlib 到底该如何控制legend的位置之一？](https://zhuanlan.zhihu.com/p/99531531)

[Python 作图实现坐标轴截断（打断）](https://blog.csdn.net/maryyu8873/article/details/84313423)

