---
layout:     post
title:      "强化学习思考（11）Advanced Policy Gradient"
subtitle:    "Advanced Policy Gradient"
date:       2020-04-23 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
mathjax: true
tags:
    - 强化学习
    - 强化学习思考
---



关于 Advanced Policy Gradient 的注意事项。



## 目录

- [强化学习思考（1）前言](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-1-%E5%89%8D%E8%A8%80/)
- [强化学习思考（2）强化学习简介](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-2-%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E7%AE%80%E4%BB%8B/)
- [强化学习思考（3）马尔可夫决策过程](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-3-%E9%A9%AC%E5%B0%94%E5%8F%AF%E5%A4%AB%E5%86%B3%E7%AD%96%E8%BF%87%E7%A8%8B/)
- [强化学习思考（4）模仿学习和监督学习](https://liushunyu.github.io/2020/04/13/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-4-%E6%A8%A1%E4%BB%BF%E5%AD%A6%E4%B9%A0%E5%92%8C%E7%9B%91%E7%9D%A3%E5%AD%A6%E4%B9%A0/)
- [强化学习思考（5）动态规划](https://liushunyu.github.io/2020/04/15/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-5-%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92/)
- [强化学习思考（6）蒙特卡罗和时序差分](https://liushunyu.github.io/2020/04/15/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-6-%E8%92%99%E7%89%B9%E5%8D%A1%E7%BD%97%E5%92%8C%E6%97%B6%E5%BA%8F%E5%B7%AE%E5%88%86/)
- [强化学习思考（7）策略梯度](https://liushunyu.github.io/2020/04/18/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-7-%E7%AD%96%E7%95%A5%E6%A2%AF%E5%BA%A6/)
- [强化学习思考（8）Actor-Critic 方法](https://liushunyu.github.io/2020/04/21/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-8-Actor-Critic-%E6%96%B9%E6%B3%95/)
- [强化学习思考（9）值函数方法](https://liushunyu.github.io/2020/04/22/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-9-%E5%80%BC%E5%87%BD%E6%95%B0%E6%96%B9%E6%B3%95/)
- [强化学习思考（10）Deep Q Network](https://liushunyu.github.io/2020/04/22/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-10-Deep-Q-Network/)
- 强化学习思考（11）Advanced Policy Gradient



## From on-policy to off-policy

### 基本术语

#### On-policy

学习的 agent 以及和环境进行互动的 agent 是**同一个agent**。



#### Off-policy

学习的 agent 以及和环境进行互动的 agent 是**不同的agent**。



**为什么要引入 Off-policy**

如果我们使用 $\pi_\theta$  来收集数据，那么参数 $\theta$ 被更新后，我们需要重新对训练数据进行采样，这样会造成巨大的时间消耗。而利用 $\pi_{\theta'}$ 来进行采样，将采集的样本拿来训练 $\theta$，$\theta'$是固定的，采集的样本可以被重复使用。



### Important sampling

我们可以使用 $q$ 分布来计算 $p$ 分布期望值。


$$
E_{x \sim p}[f(x)] =\int f(x) p(x) d x=\int f(x) \frac{p(x)}{q(x)} q(x) d x=E_{x \sim q}[f(x) \frac{p(x)}{q(x)}]
$$


需要注意的是，两个分布 $p$，$q$ 之间的差别不能太大，否则方差会出现较大的差别。


$$
\begin{aligned} \operatorname{Var}_{x \sim q}\left[f(x) \frac{p(x)}{q(x)}\right] &=E_{x \sim q}\left[\left(f(x) \frac{p(x)}{q(x)}\right)^{2}\right]-\left(E_{x \sim q}\left[f(x) \frac{p(x)}{q(x)}\right]\right)^{2} 

\\ &=\int \left[\left(f(x) \frac{p(x)}{q(x)}\right)^{2} q(x)\right] d x-\left(E_{x \sim p}[f(x)]\right)^{2}

\\ &=\int \left[\left(f(x) \frac{p(x)}{q(x)}\right)^{2} \frac{q(x)}{p(x)}p(x)\right] d x-\left(E_{x \sim p}[f(x)]\right)^{2}

\\ &=E_{x \sim p}\left[f(x)^{2} \frac{p(x)}{q(x)}\right]-\left(E_{x \sim p}[f(x)]\right)^{2} \end{aligned}
$$



可以发现两者得出的方差表达式后面一项相同，主要差别在于前面那一项，如果分布 $p$ 和 $q$ 之间差别太大，会导致第一项的值较大或较小，于是造成两者较大的差别。

如果分布 $p$ 和 $q$ 之间的差别过大，在训练的过程中就需要进行更多次数的采样，但这样会导致在采样上耗费较大的时间。



### 目标函数

使用 $\pi_{\theta'}$ 来采样多个回合的 $\tau$ 作为模型输入，然后对 Important sampling 后的 Expected Reward  进行求导。


$$
J(\theta)=  E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[\frac{\pi_{\theta}(\tau)}{\pi_{\theta^{\prime}}(\tau)} r(\tau) \right]
$$



其中


$$
\frac{\pi_{\theta}(\tau)}{\pi_{\theta^{\prime}}(\tau)} 

=\frac{p\left(s_{1}\right) \prod_{t=1}^{T} \pi_{\theta}\left(a_{t} | s_{t}\right) p\left(s_{t+1} | s_{t}, a_{t}\right)}{p\left(s_{1}\right) \prod_{t=1}^{T} \pi_{\theta'}\left(a_{t} | s_{t}\right) p\left(s_{t+1} | s_{t}, a_{t}\right)}

= \frac{\prod_{t=1}^{T} \pi_{\theta}\left(a_{t} | s_{t}\right) }{\prod_{t=1}^{T} \pi_{\theta'}\left(a_{t} | s_{t}\right) }
$$




### 梯度更新

对目标函数求梯度得


$$
\nabla_{\theta} J(\theta)=  E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[\frac{\nabla_{\theta}\pi_{\theta}(\tau)}{\pi_{\theta^{\prime}}(\tau)} r(\tau) \right] = E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[ \frac{\pi_{\theta}(\tau)}{\pi_{\theta^{\prime}}(\tau)}  \nabla_{\theta}\log \pi_{\theta}(\tau) r(\tau) \right]
\\
=E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[ \left( \frac{\prod_{t=1}^{T} \pi_{\theta}\left(a_{t} | s_{t}\right) }{\prod_{t=1}^{T} \pi_{\theta'}\left(a_{t} | s_{t}\right) } \right)  \left(\sum_{t=1}^{T} \nabla_\theta \log \pi_{\theta}\left(a_{i,t} | o_{i,t}\right) \right) \left(\sum_{t=1}^{T}   r\left({s}_{i,t}, {a}_{i,t}\right) \right) \right]
$$


如果 $\pi_{\theta}(\tau) = \pi_{\theta^{\prime}}(\tau)$，则与 on-policy 的策略梯度一致。

目前存在一个问题便是重要性采样中的累乘可能会导致该值特别的小，下面介绍两种方法：



#### 修改累乘计算方式

同修改累积奖赏 reward 计算方式类似，在时间 $t$ 采取的行动 action 与 $t$ 时期之前的奖赏 reward 无关，在时间 $t$ 的重要性采样权重与 $t$ 时期之后的动作 action 无关。


$$
\require{cancel}
\nabla_{\theta} J(\theta)
=E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[ \left( \frac{\prod_{t=1}^{T} \pi_{\theta}\left(a_{t} | s_{t}\right) }{\prod_{t=1}^{T} \pi_{\theta'}\left(a_{t} | s_{t}\right) } \right)  \left(\sum_{t=1}^{T} \nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \right) \left(\sum_{t=1}^{T}   r\left({s}_{i,t}, {a}_{i,t}\right) \right) \right]
\\
\to E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[   \sum_{t=1}^{T} \left(\nabla_\theta \log \pi_{\theta}\left(a_{t} | s_{t}\right) \left( \prod_{t'=1}^{t}\frac{ \pi_{\theta}\left(a_{t'} | s_{t'}\right) }{ \pi_{\theta'}\left(a_{t'} | s_{t'}\right) } \right)\left(\sum_{t'=t}^{T}   r\left({s}_{t'}, {a}_{t'}\right) \cancel{\left( \prod_{t''=t}^{t'} \frac{ \pi_{\theta}\left(a_{t''} | s_{t''}\right) }{\pi_{\theta'}\left(a_{t''} | s_{t''}\right) } \right)} \right)\right)\right]
$$


忽略后一项可以得到策略迭代算法，这在后面会讲。



#### A first-order approximation for IS

**on-policy**

目标函数为


$$
J(\theta) = \sum_{t=1}^{T} E_{\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right) \sim p_{\theta}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)}\left[r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]
$$



求导结果如下


$$
\nabla_{\theta} J_{PG}(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \sum_{t'=t}^{T}   r\left({s}_{i,t'}, {a}_{i,t'}\right) \right]
$$


**off-policy**

在两个层面上做重要性抽样


$$
J(\theta) = \sum_{t=1}^{T} E_{\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right) \sim p_{\theta}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)}\left[r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]
\\ = \sum_{t=1}^{T} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t} \right)}\left[r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]]
\\ = \sum_{t=1}^{T} E_{\mathbf{s}_{t} \sim p_{\theta'}\left(\mathbf{s}_{t}\right)}\left[\frac{ p_{\theta}\left(s_{t}\right) }{p_{\theta'}\left(s_{t}\right) }E_{\mathbf{a}_{t} \sim \pi_{\theta'}\left(\mathbf{a}_{t} | \mathbf{s}_{t} \right)}\left[\frac{ \pi_{\theta}\left(a_{t} | s_{t}\right) }{\pi_{\theta'}\left(a_{t} | s_{t}\right) }r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
$$


求导结果如下


$$
\require{cancel}
\nabla_{\theta} J_{PG}(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\cancel{\frac{ p_{\theta}\left(s_{i,t}\right) }{p_{\theta'}\left(s_{i,t}\right) }}\frac{ \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) }{\pi_{\theta'}\left(a_{i,t} | s_{i,t}\right) } \nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right)  \sum_{t'=t}^{T}   r\left({s}_{i,t'}, {a}_{i,t'}\right) \right]
$$


虽然删除第一层重要性采样会引入一定的偏差，当两个策略非常接近的情况下，某一个状态 state 出现的概率几乎没有差别，因此可以将这一项近似地消掉。



Policy gradient as policy iteration $\quad J(\theta)=E_{\tau \sim p_{\theta}(\tau)}\left[\sum_{t} \gamma^{t} r\left(s_{t}, \mathbf{a}_{t}\right)\right]$
$\begin{aligned} J\left(\theta^{\prime}\right)-J(\theta) &=J\left(\theta^{\prime}\right)-E_{\mathrm{s}_{0} \sim p\left(\mathbf{s}_{0}\right)}\left[V^{\pi_{\theta}}\left(\mathbf{s}_{0}\right)\right] & & \\ &=J\left(\theta^{\prime}\right)-E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[V^{\pi_{\theta}}\left(\mathbf{s}_{0}\right)\right] & & \text { claim: } J\left(\theta^{\prime}\right)-J(\theta)=E_{\tau \sim p_{\theta^{\prime}}(\tau)} &\left[\sum_{t} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right] \end{aligned}$
$$
\begin{aligned}
&=J\left(\theta^{\prime}\right)-E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)-\sum_{t=1}^{\infty} \gamma^{t} V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right] \\
&=J\left(\theta^{\prime}\right)+E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t}\left(\gamma V^{\pi_{\theta}}\left(\mathbf{s}_{t+1}\right)-V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right)\right] \\
&=E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]+E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t}\left(\gamma V^{\pi_{\theta}}\left(\mathbf{s}_{t+1}\right)-V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right)\right] \\
&=E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t}\left(r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)+\gamma V^{\pi_{\theta}}\left(\mathbf{s}_{t+1}\right)-V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right)\right] \\
&=E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]
\end{aligned}
$$


## 参考资料及致谢

所有参考资料在前言中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

