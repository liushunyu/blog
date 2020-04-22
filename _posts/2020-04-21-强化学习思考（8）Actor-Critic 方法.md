---
layout:     post
title:      "强化学习思考（8）Actor-Critic 方法"
subtitle:    "Actor-Critic 方法"
date:       2020-04-21 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
mathjax: true
tags:
    - 强化学习
    - 强化学习思考
---



关于 Actor-Critic 方法的注意事项。



## 目录

- [强化学习思考（1）前言](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-1-%E5%89%8D%E8%A8%80/)
- [强化学习思考（2）强化学习简介](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-2-%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E7%AE%80%E4%BB%8B/)
- [强化学习思考（3）马尔可夫决策过程](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-3-%E9%A9%AC%E5%B0%94%E5%8F%AF%E5%A4%AB%E5%86%B3%E7%AD%96%E8%BF%87%E7%A8%8B/)
- [强化学习思考（4）模仿学习和监督学习](https://liushunyu.github.io/2020/04/13/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-4-%E6%A8%A1%E4%BB%BF%E5%AD%A6%E4%B9%A0%E5%92%8C%E7%9B%91%E7%9D%A3%E5%AD%A6%E4%B9%A0/)
- [强化学习思考（5）动态规划](https://liushunyu.github.io/2020/04/15/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-5-%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92/)
- [强化学习思考（6）蒙特卡罗和时序差分](https://liushunyu.github.io/2020/04/15/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-6-%E8%92%99%E7%89%B9%E5%8D%A1%E7%BD%97%E5%92%8C%E6%97%B6%E5%BA%8F%E5%B7%AE%E5%88%86/)
- [强化学习思考（7）策略梯度](https://liushunyu.github.io/2020/04/18/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-7-%E7%AD%96%E7%95%A5%E6%A2%AF%E5%BA%A6/)
- [强化学习思考（8）Actor-Critic 方法](https://liushunyu.github.io/2020/04/21/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-8-Actor-Critic-%E6%96%B9%E6%B3%95/)



## 策略梯度

1、sample $\tau_{i}$ from $\pi_{\theta}$ (run the policy)

2、compute the gradient



$$
\nabla_{\theta} J_{PG}(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \left(\sum_{t'=t}^{T}   r\left({s}_{i,t'}, {a}_{i,t'}\right) - b\right) \right]
$$



3、$\theta \leftarrow \theta+\alpha \nabla_{\theta} J(\theta)$



### reward to go

$\hat{Q}(s_t,a_t) $: estimate of expect reward if we take action $a_{t}$ in state $s_{t}$

- 无偏估计
- 方差大：使用了仅仅一个样本轨迹


$$
\hat{Q}(s_t,a_t) = \sum_{t'=t}^{T}   r\left({s}_{t'}, {a}_{t'}\right)
$$


$Q(s_t,a_t)$: true expect reward to go

- 无偏估计
- 方差小：使用了所有样本轨迹的期望


$$
Q(s_t,a_t) = \sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}, {a}_{t}]
$$


baseline: average reward


$$
b= \frac{1}{N} \sum_i Q(s_{i,t},a_{i,t})
$$


baseline: state value function


$$
b=V(s_t) = E_{a_t \sim \pi_{\theta}}Q(s_{t},a_{t})
$$


### Advantage function

state-action value function: total reward from taking action $a_{t}$ in state $s_{t}$


$$
Q^\pi(s_t,a_t) = \sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}, {a}_{t}]
$$


state value function: total reward from state $s_{t}$


$$
V^\pi(s_t) = E_{a_t \sim \pi_{\theta}}[Q(s_{t},a_{t})]
$$


Advantage function: how much better action $a_{t}$ is


$$
A^\pi(s_t,a_t) = Q^\pi(s_t,a_t) - V^\pi(s_t)
$$


## Actor-Critic 方法

compute the gradient



$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) A^\pi(s_{i,t},a_{i,t}) \right]
$$



we should estimate Advantage function



### Value function fitting

Q and V


$$
\begin{aligned}
& Q^\pi(s_t,a_t)
\\=& \sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}, {a}_{t}] 
\\ =& r(s_t,a_t) + \sum_{t'=t+1}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}, {a}_{t}]
\\ =& r(s_t,a_t) + E_{s_{t+1} \sim p(s_{t+1}|s_t,a_t)}[V^\pi(s_{t+1})]
\\ \approx & r(s_t,a_t) + V^\pi(s_{t+1})
\end{aligned}
$$


V and A


$$
A^\pi(s_t,a_t) \approx r(s_t,a_t) + V^\pi(s_{t+1}) - V^\pi(s_t)
$$


we can estimate state value function to get advantage function



### Policy evaluation

we use a network $V^\pi_{\phi}(s_t)$ to approximate state value function $V^\pi(s_t)$


$$
\begin{aligned}
& V^\pi(s_t) 
\\=&\; E_{a_t \sim \pi_{\theta}}[Q(s_{t},a_{t})]
\\=&\; E_{a_t \sim \pi_{\theta}}\left[\sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}, {a}_{t}] \right]
\\=&\; \sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}]
\end{aligned}
$$


#### Monte Carlo evaluation with function approximation

use sampled reward sums to train network


$$
\begin{aligned}
& V^\pi(s_t) 
\\=&\; \sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}]
\\ \approx&\;\sum_{t'=t}^{T}   r\left({s}_{t'}, {a}_{t'}\right)
\end{aligned}
$$


target


$$
y_{i,t} = \sum_{t'=t}^{T}   r\left({s}_{t'}, {a}_{t'}\right)
$$


#### Temporal difference evaluation with function approximation

use bootstrap to train network


$$
\begin{aligned}
& V^\pi(s_t) 
\\=&\; \sum_{t'=t}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}]
\\ \approx&\; r(s_t,a_t) + \sum_{t'=t+1}^T E_{\pi_\theta}[r\left({s}_{t'}, {a}_{t'}\right) | {s}_{t}]
\\ \approx&\; r(s_t,a_t) + V^\pi(s_{t+1})
\end{aligned}
$$


traget


$$
y_{i,t} = r(s_t,a_t) + V^\pi_{\phi}(s_{t+1})
$$


### Actor-critic algorithms

#### Batch Actor-critic algorithms

1、sample $(s_i,a_i)$ from $\pi_\theta(a \mid s)$

2、fit $\hat{V}_{\phi}^{\pi}(\mathbf{s})$ to sampled reward sums

3、evaluate


$$
\hat{A}^{\pi}\left(\mathbf{s}_{i}, \mathbf{a}_{i}\right)=r\left(\mathbf{s}_{i}, \mathbf{a}_{i}\right)+\gamma \hat{V}_{\phi}^{\pi}\left(\mathbf{s}_{i}^{\prime}\right)-\hat{V}_{\phi}^{\pi}\left(\mathbf{s}_{i}\right)
$$


4、compute the gradient


$$
\nabla_{\theta} J(\theta) \approx \sum_{i} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{i} \mid \mathbf{s}_{i}\right) \hat{A}^{\pi}\left(\mathbf{s}_{i}, \mathbf{a}_{i}\right)
$$


5、$\theta \leftarrow \theta+\alpha \nabla_{\theta} J(\theta)$



#### Online Actor-critic algorithms

1、take action $\mathbf{a} \sim \pi_{\theta}(\mathbf{a} \mid \mathbf{s}),$ get $\left(\mathbf{s}, \mathbf{a}, \mathbf{s}^{\prime}, r\right)$

2、update using target


$$
\hat{V}_{\phi}^{\pi} \leftarrow r+\gamma\hat{V}_{\phi}^{\pi}(\mathbf{s}^{\prime})
$$


3、evaluate


$$
\hat{A}^{\pi}(\mathbf{s}, \mathbf{a})=r(\mathbf{s}, \mathbf{a})+\gamma \hat{V}_{\phi}^{\pi}\left(\mathbf{s}^{\prime}\right)-\hat{V}_{\phi}^{\pi}(\mathbf{s})
$$



4、compute the gradient



$$
\nabla_{\theta} J(\theta) \approx \nabla_{\theta} \log \pi_{\theta}(\mathbf{a} \mid \mathbf{s}) \hat{A}^{\pi}(\mathbf{s}, \mathbf{a})
$$



5、$\theta \leftarrow \theta+\alpha \nabla_{\theta} J(\theta)$



### Architecture design

1、two network design & shared network design

- two network design: 
  - simple & stable
- shared network design: 
  - shared features between actor & critic
  - 训练比较难，因为会有两个不同的梯度（策略梯度和回归梯度）往不同的方向更新共享的参数，数据类型也不太一样，因此让这个网络稳定下来需要很多技巧，如初始化数值和学习率的选择。
  - 如果模拟器（如 Atari 模拟器和 MuJoCo）很快的话，不妨使用双网络结构，这样比较容易。



2、使用 output entropy 作为 $\pi(s)$ 的正则项的时候，entropy 越大，不同动作的概率越接近，越鼓励探索。



3、synchronized parallel actor-critic & asynchronous parallel actor-critic

<img width="480" src="/img/in-post/2020-04-21-强化学习思考（8）Actor-Critic 方法.assets/image-20190820121007989.png"/>

step 1：每个 worker 都会 copy 全局参数

step 2：每个 worker 都与环境进行互动，并得到 sample data

step 3：每个 worker 计算梯度

step 4：每个 worker 将计算得到的梯度返回给 global network 用于更新全局参数，即使全局参数已被其他 worker 更新也没有关系。



##  discount factors

### Temporal difference policy gradient


$$
y_{i,t} = r(s_t,a_t) + V^\pi_{\phi}(s_{t+1})
$$


when $T$ (episode length) is $\infty$, $V^\pi_{\phi}(s_{t+1})$ can get infinitely large in many case

we can use a discount factor $\gamma \in [0,1]$ (0.99 works well): better to get rewards sooner than later


$$
y_{i,t} = r(s_t,a_t) + \gamma V^\pi_{\phi}(s_{t+1})
$$


另外的角度：$\gamma$ 改变了 MDP（添加了终止状态），因为 state value function 本来是对总收益求期望，使用了 discount factors 可以认为有 $\gamma$ 的概率获得原先的总收益，$1-\gamma$ 的概率进入终止状态不获得收益。

<img width="480" src="/img/in-post/2020-04-21-强化学习思考（8）Actor-Critic 方法.assets/image-20200421210203987.png"/>



compute the gradient



$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \left( r(s_{i,t},a_{i,t}) + \gamma V^\pi_{\phi}(s_{i,t+1})- V^\pi_{\phi}(s_{i,t}) \right) \right]
$$




### Monte Carlo policy gradient


$$
y_{i,t} = \sum_{t'=t}^{T}   r\left({s}_{t'}, {a}_{t'}\right)
$$


有两种加 discount factors 计算梯度的形式

Further reading: Philip Thomas, Bias in natural actor-critic algorithms. ICML 2014



#### option 1

一般选择 option 1


$$
\begin{aligned}
&\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{i, t} | \mathbf{s}_{i, t}\right)\left(\sum_{t^{\prime}=t}^{T} \gamma^{t^{\prime}-t} r\left(\mathbf{s}_{i, t^{\prime}}, \mathbf{a}_{i, t^{\prime}}\right)\right)\\
\end{aligned}
$$


#### option 2

option 2 不仅对 reward 的计算进行 dicount，也对梯度的计算进行了 discount，会使得越往后的步骤的梯度影响力越小。


$$
\begin{aligned}
\nabla_{\theta} J(\theta) &\approx \frac{1}{N} \sum_{i=1}^{N}\left(\sum_{t=1}^{T} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{i, t} | \mathbf{s}_{i, t}\right)\right)\left(\sum_{t=1}^{T} \gamma^{t-1} r\left(\mathbf{s}_{i, t^{\prime}}, \mathbf{a}_{i, t^{\prime}}\right)\right)\\
&\approx \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{i, t} | \mathbf{s}_{i, t}\right)\left(\sum_{t'=t}^{T} \gamma^{t^{\prime}-1} r\left(\mathbf{s}_{i, t^{\prime}}, \mathbf{a}_{i, t^{\prime}}\right)\right)\\
&= \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} \gamma^{t-1} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{i, t} | \mathbf{s}_{i, t}\right)\left(\sum_{t^{\prime}=t}^{T} \gamma^{t^{\prime}-t} r\left(\mathbf{s}_{i, t^{\prime}}, \mathbf{a}_{i, t^{\prime}}\right)\right)
\end{aligned}
$$


## baselines

### state-dependent baselines

**policy gradient**


$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \left(\sum_{t'=t}^{T}   \gamma^{t'-t}r\left({s}_{i,t'}, {a}_{i,t'}\right) - b\right) \right]
$$


- no bias
- higher variance (because single-sample estimate)



**Actor-critic**


$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \left( r(s_{i,t},a_{i,t}) + \gamma V^\pi_{\phi}(s_{i,t+1})- V^\pi_{\phi}(s_{i,t}) \right) \right]
$$


- lower variance (due to critic)
- not unbiased (if the critic is not perfect)



**use $V^\pi_{\phi}(s_{i,t})$ and still keep the estimator unbiased**


$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \left(\sum_{t'=t}^{T}   \gamma^{t'-t}r\left({s}_{i,t'}, {a}_{i,t'}\right) - V^\pi_{\phi}(s_{i,t})\right) \right]
$$


- no bias
- lower variance (baseline is closer to rewards)
- but still higher variance (because single-sample estimate)



### action-dependent baselines

use a critic without the bias (still unbiased), provided second term can be evaluated Gu et al. 2016 (Q-Prop)


$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N}  \sum_{t=1}^{T} \left[\nabla_\theta \log \pi_{\theta}\left(a_{i,t} | s_{i,t}\right) \left(\sum_{t'=t}^{T}   \gamma^{t'-t}r\left({s}_{i,t'}, {a}_{i,t'}\right) - Q^\pi_{\phi}(s_{i,t},a_{i,t})\right) \right]
$$


- goes to zero in expectation if critic is correct
- not correct


$$
\nabla_{\theta} J(\theta) \approx \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{i, t} | \mathbf{s}_{i, t}\right)\left(\hat{Q}_{i, t}-Q_{\phi}^{\pi}\left(\mathbf{s}_{i, t}, \mathbf{a}_{i, t}\right)\right)+\frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta} E_{\mathbf{a} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{i, t}\right)}\left[Q_{\phi}^{\pi}\left(\mathbf{s}_{i, t}, \mathbf{a}_{t}\right)\right]
$$


## Generalized advantage estimation

**n-step returns**


$$
\hat{A}_{n}^{\pi}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)=\sum_{t^{\prime}=t}^{t+n} \gamma^{t^{\prime}-t} r\left(\mathbf{s}_{t^{\prime}}, \mathbf{a}_{t^{\prime}}\right)-\hat{V}_{\phi}^{\pi}\left(\mathbf{s}_{t}\right)+\gamma^{n} \hat{V}_{\phi}^{\pi}\left(\mathbf{s}_{t+n}\right)
$$


**Generalized advantage estimation**


$$
\hat{A}_{\mathrm{GAE}}^{\pi}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)=\sum_{n=1}^{\infty} w_{n} \hat{A}_{n}^{\pi}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)
$$


where $w_{n} \propto \lambda^{n-1}$


$$
\hat{A}_{\mathrm{GAE}}^{\pi}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)=\sum_{t^{\prime}=t}^{\infty}(\gamma \lambda)^{t^{\prime}-t} \delta_{t^{\prime}}
\\
\delta_{t^{\prime}}=r\left(\mathbf{s}_{t^{\prime}}, \mathbf{a}_{t^{\prime}}\right)+\gamma \hat{V}_{\phi}^{\pi}\left(\mathbf{s}_{t^{\prime}+1}\right)-\hat{V}_{\phi}^{\pi}\left(\mathbf{s}_{t^{\prime}}\right)
$$


$(\gamma \lambda)$ similar effect as discount, and discount = variance reduction



## Compatible Function Approximation

> Theorem (Compatible Function Approximation Theorem)
>
> If the following two conditions are satisfied:
>
> 1、Value function approximator is compatible to the policy
>
> 
> $$
> \nabla_{w} Q_{w}(s, a)=\nabla_{\theta} \log \pi_{\theta}(s, a)
> $$
> 
>
> 2、Value function parameters $w$ minimise the mean-squared error
>
> 
> $$
> \varepsilon=\mathbb{E}_{\pi_{\theta}}\left[\left(Q^{\pi_{\theta}}(s, a)-Q_{w}(s, a)\right)^{2}\right]
> $$
> 
>
> Then the policy gradient is exact,
>
> 
> $$
> \nabla_{\theta} J(\theta)=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) Q_{w}(s, a)\right]
> $$
> 



If $w$ is chosen to minimise mean-squared error, gradient of $\varepsilon$ w.r.t. $w$ must be zero,


$$
\begin{aligned}
\nabla_{w} \varepsilon&=0 \\
\mathbb{E}_{\pi_{\theta}}\left[\left(Q^{\theta}(s, a)-Q_{w}(s, a)\right) \nabla_{w} Q_{w}(s, a)\right]&=0 \\
\mathbb{E}_{\pi_{\theta}}\left[\left(Q^{\theta}(s, a)-Q_{w}(s, a)\right) \nabla_{\theta} \log \pi_{\theta}(s, a)\right]&=0 \\
\mathbb{E}_{\pi_{\theta}}\left[Q^{\theta}(s, a) \nabla_{\theta} \log \pi_{\theta}(s, a)\right]&=\mathbb{E}_{\pi_{\theta}}\left[Q_{w}(s, a) \nabla_{\theta} \log \pi_{\theta}(s, a)\right]
\end{aligned}
$$


So $Q_{w}(s, a)$ can be substituted directly into the policy gradient,


$$
\nabla_{\theta} J(\theta)=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) Q_{w}(s, a)\right]
$$


## Summary of Policy Gradient Algorithms

The policy gradient has many equivalent forms


$$
\begin{aligned}
\nabla_{\theta} J(\theta)&=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) v_{t}\right] \quad\quad\;\;\;\quad \text { REINFORCE }  \\
&=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) Q^{w}(s, a)\right] \quad \text { Q Actor-Critic } \\
&=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) A^{w}(s, a)\right] \quad \text { Advantage Actor-Critic } \\
&=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) \delta\right] \quad \quad \quad \;\;\;\;\text { TD Actor-Critic } \\
&=\mathbb{E}_{\pi_{\theta}}\left[\nabla_{\theta} \log \pi_{\theta}(s, a) \delta e\right] \quad \quad \quad \;\;\text { TD($\lambda$) Actor-Critic } 
\\
G_{\theta}^{-1} \nabla_{\theta} J(\theta)&=w \quad \quad \quad \quad\quad \quad \quad \quad \quad \quad \quad  \quad \;\text { NAtural Actor-Critic } 
\end{aligned}
$$


Each leads a stochastic gradient ascent algorithm

Critic uses policy evaluation (e.g. MC or TD learning) to estimate $Q^{\pi}(s, a), A^{\pi}(s, a)$ or $V^{\pi}(s)$



## 参考资料及致谢

所有参考资料在前言中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

