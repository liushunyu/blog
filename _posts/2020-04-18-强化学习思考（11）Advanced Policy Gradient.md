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

如果我们使用 $\pi_\theta$  来收集数据，那么参数 $\theta$ 被更新后，我们需要重新对训练数据进行采样，这样会造成巨大的时间消耗。而利用 $\pi_{\theta'}$ 来进行采样，将采集的样本拿来训练 $\theta$，$\theta'$ 是固定的，采集的样本可以被重复使用。



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



### 策略梯度目标函数

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




### 策略梯度梯度更新

对目标函数求梯度得



$$
\nabla_{\theta} J(\theta)=  E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[\frac{\nabla_{\theta}\pi_{\theta}(\tau)}{\pi_{\theta^{\prime}}(\tau)} r(\tau) \right] = E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[ \frac{\pi_{\theta}(\tau)}{\pi_{\theta^{\prime}}(\tau)}  \nabla_{\theta}\log \pi_{\theta}(\tau) r(\tau) \right]
\\
=E_{\tau \sim \pi_{\theta^{\prime}}(\tau)}\left[ \left( \frac{\prod_{t=1}^{T} \pi_{\theta}\left(a_{t} | s_{t}\right) }{\prod_{t=1}^{T} \pi_{\theta'}\left(a_{t} | s_{t}\right) } \right)  \left(\sum_{t=1}^{T} \nabla_\theta \log \pi_{\theta}\left(a_{i,t} | o_{i,t}\right) \right) \left(\sum_{t=1}^{T}   r\left({s}_{i,t}, {a}_{i,t}\right) \right) \right]
$$



其中存在一个问题便是重要性采样中的累乘可能会导致该值特别的小。



## Policy gradient as policy iteration

注意这里的符号与上面的相反，用于采样的是 $\pi_{\theta}$，需要更新的是 $\theta'$。

### policy improvement

策略梯度目标函数为：


$$
\quad J(\theta)=E_{\tau \sim p_{\theta}(\tau)}\left[\sum_{t} \gamma^{t} r\left(s_{t}, a_{t}\right)\right]
$$


希望证明 claim


$$
J\left(\theta^{\prime}\right)-J(\theta)=E_{\tau \sim p_{\theta^{\prime}}(\tau)} \left[\sum_{t} \gamma^{t} A^{\pi_{\theta}}\left(s_{t}, a_{t}\right)\right]
$$


policy improvement 求解目标便可表示为


$$
\theta' \leftarrow \arg \max_{\theta'} J\left(\theta^{\prime}\right)-J(\theta)
$$




证明流程如下：


$$
\begin{aligned}
J\left(\theta^{\prime}\right)-J(\theta) &=J\left(\theta^{\prime}\right)-E_{\mathrm{s}_{0} \sim p\left(\mathbf{s}_{0}\right)}\left[V^{\pi_{\theta}}\left(\mathbf{s}_{0}\right)\right] & & \\ &=J\left(\theta^{\prime}\right)-E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[V^{\pi_{\theta}}\left(\mathbf{s}_{0}\right)\right] \\
&=J\left(\theta^{\prime}\right)-E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)-\sum_{t=1}^{\infty} \gamma^{t} V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right] \\
&=J\left(\theta^{\prime}\right)+E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t}\left(\gamma V^{\pi_{\theta}}\left(\mathbf{s}_{t+1}\right)-V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right)\right] \\
&=E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]+E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t}\left(\gamma V^{\pi_{\theta}}\left(\mathbf{s}_{t+1}\right)-V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right)\right] \\
&=E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t}\left(r\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)+\gamma V^{\pi_{\theta}}\left(\mathbf{s}_{t+1}\right)-V^{\pi_{\theta}}\left(\mathbf{s}_{t}\right)\right)\right] \\
&=E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]
\end{aligned}
$$


## Policy gradient with Important sampling

我们希望去求解 policy improvement 目标，因为是 off-policy 的方法，所以需要重要性采样，我们在两个层面上做重要性抽样



$$
J\left(\theta^{\prime}\right)-J(\theta) = E_{\tau \sim p_{\theta^{\prime}}(\tau)}\left[\sum_{t=0}^{\infty} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\\ 
= \sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta'}\left(\mathbf{s}_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta'}\left(\mathbf{a}_{t} | \mathbf{s}_{t} \right)}\left[\gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]\\ 
= \sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}\left[\frac{ p_{\theta'}\left(s_{t}\right) }{p_{\theta}\left(s_{t}\right) }E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t} \right)}\left[\frac{ \pi_{\theta'}\left(a_{t} | s_{t}\right) }{\pi_{\theta}\left(a_{t} | s_{t}\right) }\gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]\\
$$



其中 $\frac{ p_{\theta'}\left(s_{t}\right) }{p_{\theta}\left(s_{t}\right) }$ 是比较难求的，我们希望能直接将这一项消除，下面进行证明



### Bounding the distribution change

希望证明 Claim: 

- $p_{\theta}\left(\mathbf{s}_{t}\right)$ is close to $p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)$ when $\pi_{\theta}$ is close to $\pi_{\theta^{\prime}}$



#### Simple case

**assume**

- assume $\pi_{\theta}$ is a deterministic policy $\mathbf{a}_{t}=\pi_{\theta}\left(\mathbf{s}_{t}\right)$
- assume $\pi_{\theta^{\prime}}$ is close to $\pi_{\theta}$ if 


$$
\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} \neq \pi_{\theta}\left(\mathbf{s}_{t}\right) | \mathbf{s}_{t}\right) \leq \epsilon
$$


可以得到


$$
\left.p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)=(1-\epsilon)^{t} p_{\theta}\left(\mathbf{s}_{t}\right)+\left(1-(1-\epsilon)^{t}\right)\right) p_{\text {mistake }}\left(\mathbf{s}_{t}\right)
$$


可证明两个分布之间足够小


$$
\left|p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)-p_{\theta}\left(\mathbf{s}_{t}\right)\right|=\left(1-(1-\epsilon)^{t}\right)\left|p_{\text {mistake }}\left(\mathbf{s}_{t}\right)-p_{\theta}\left(\mathbf{s}_{t}\right)\right| 
\\ \leq 2\left(1-(1-\epsilon)^{t}\right) \leq 2 \epsilon t
$$


useful identity: $(1-\epsilon)^{t} \geq 1-\epsilon t$ for $\epsilon \in[0,1]$



#### General case

**assume**

- assume $\pi_{\theta}$ is an arbitrary distribution
- assume $\pi_{\theta^{\prime}}$ is close to $\pi_{\theta}$  for all $\mathbf{s}_{t}$ if 


$$
\left|\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)-\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right| \leq \epsilon
$$


**lemma**

- if $\left|p_{X}(x)-p_{Y}(x)\right|=\epsilon$, exists $p(x, y)$ such that $p(x)=p_{X}(x)$ and $p(y)=p_{Y}(y)$ and $p(x=y)=1-\epsilon$ 
- $\Rightarrow p_{X}(x)$ "agrees" with $p_{Y}(y)$ with probability $\epsilon$ 
- $\Rightarrow \pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)$ takes a different action than $\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)$ with probability at most $\epsilon$



同理可以得到
$$
\left|p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)-p_{\theta}\left(\mathbf{s}_{t}\right)\right|=\left(1-(1-\epsilon)^{t}\right)\left|p_{\text {mistake }}\left(\mathbf{s}_{t}\right)-p_{\theta}\left(\mathbf{s}_{t}\right)\right| 
\\ \leq 2\left(1-(1-\epsilon)^{t}\right) \leq 2 \epsilon t
$$


### Bounding the objective value

根据前面的证明可以得到

- when $\pi_{\theta^{\prime}}$ is close to $\pi_{\theta}$  for all $\mathbf{s}_{t}$ if 


$$
\left|\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)-\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right| \leq \epsilon
$$

- $p_{\theta}\left(\mathbf{s}_{t}\right)$ is close to $p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)$


$$
\left|p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)-p_{\theta}\left(\mathbf{s}_{t}\right)\right| \leq 2 \epsilon t
$$


首先可以证明


$$
\begin{aligned}
E_{p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)}\left[f\left(\mathbf{s}_{t}\right)\right]&=\sum_{\mathbf{s}_{t}} p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right) f\left(\mathbf{s}_{t}\right) 
\\&=\sum_{\mathbf{s}_{t}} (p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right) + p_{\theta}\left(\mathbf{s}_{t}\right) - p_{\theta}\left(\mathbf{s}_{t}\right))f\left(\mathbf{s}_{t}\right) 
\\& \geq \sum_{\mathbf{s}_{t}} p_{\theta}\left(\mathbf{s}_{t}\right) f\left(\mathbf{s}_{t}\right)-\left|p_{\theta}\left(\mathbf{s}_{t}\right)-p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)\right| \max _{\mathbf{s}_{t}} f\left(\mathbf{s}_{t}\right) \\
& \geq E_{p_{\theta}\left(\mathbf{s}_{t}\right)}\left[f\left(\mathbf{s}_{t}\right)\right]-2 \epsilon t \max _{\mathbf{s}_{t}} f\left(\mathbf{s}_{t}\right)
\end{aligned}
$$


因此可得


$$
\sum_{t} E_{s_{t} \sim p_{\theta^{\prime}}\left(s_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right] \geq 
\\
\sum_{t} E_{s_{t} \sim p_{\theta}\left(s_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]-\underbrace{\sum_{t} 2 \epsilon t C}_{O\left(T r_{\max }\right) \text { or } O\left(\frac{r_{\max }}{1-\gamma}\right)}
$$


### Final Objective

求解函数如下


$$
\theta^{\prime} \leftarrow \arg \max _{\theta^{\prime}} \sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
$$


such that


$$
\left|\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)-\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right| \leq \epsilon
$$


for small enough $\epsilon,$ this is guaranteed to improve $J\left(\theta^{\prime}\right)-J(\theta)$



## KL divergence Bounding

我们使用 KL divergence 替换原来的相似度度量方法


$$
D_{\mathrm{KL}}\left(p_{1}(x) \| p_{2}(x)\right)=E_{x \sim p_{1}(x)}\left[\log \frac{p_{1}(x)}{p_{2}(x)}\right]
$$


- when $\pi_{\theta^{\prime}}$ is close to $\pi_{\theta}$  for all $\mathbf{s}_{t}$ if 


$$
D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right) \leq \epsilon
$$


因为


$$
\left|\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)-\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right| \leq \sqrt{\frac{1}{2} D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right)}
$$


所以仍然可证明得

- $p_{\theta}\left(\mathbf{s}_{t}\right)$ is close to $p_{\theta^{\prime}}\left(\mathbf{s}_{t}\right)$



### New Objective

求解函数如下


$$
\theta^{\prime} \leftarrow \arg \max _{\theta^{\prime}} \sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
$$


such that


$$
D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right) \leq \epsilon
$$


for small enough $\epsilon,$ this is guaranteed to improve $J\left(\theta^{\prime}\right)-J(\theta)$



### optimize the objective

#### Dual gradient descent

使用拉格朗日乘子法修改目标函数为


$$
\mathcal{L}\left(\theta^{\prime}, \lambda\right)=\sum_{t} E_{\mathrm{s}_{t} \sim p_{\theta}\left(\mathrm{s}_{t}\right)}\left[E_{\mathrm{a}_{t} \sim \pi_{\theta}\left(\mathrm{a}_{t} | \mathrm{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{S}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
\\-\lambda\left(D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right)-\epsilon\right)
$$




- step 1、Maximize $\mathcal{L}\left(\theta^{\prime}, \lambda\right)$ with respect to $\theta^{\prime}$ (can do this incompletely for a few grad steps)

- step 2、$\lambda \leftarrow \lambda+\alpha\left(D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right)-\epsilon\right)$



Intuition: raise $\lambda$ if constraint violated too much, else lower it an instance of dual gradient descent



#### first order Taylor approximation

定义


$$
\bar{A}(\theta') = \sum_{t} E_{\mathrm{s}_{t} \sim p_{\theta}\left(\mathrm{s}_{t}\right)}\left[E_{\mathrm{a}_{t} \sim \pi_{\theta}\left(\mathrm{a}_{t} | \mathrm{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{S}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
$$


求 $\bar{A}(\theta')$ 在 $\theta$ 初的一阶泰勒展开，修改目标函数为


$$
\theta^{\prime} \leftarrow \arg \max _{\theta^{\prime}} \nabla_{\theta'} \bar{A}(\theta)^{T}\left(\theta^{\prime}-\theta\right)
$$


such that $D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right) \leq \epsilon$



#### Policy Gradient

定义


$$
\bar{A}(\theta') = \sum_{t} E_{\mathrm{s}_{t} \sim p_{\theta}\left(\mathrm{s}_{t}\right)}\left[E_{\mathrm{a}_{t} \sim \pi_{\theta}\left(\mathrm{a}_{t} | \mathrm{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{S}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
$$


求解梯度得


$$
\nabla_{\theta^{\prime}} \bar{A}\left(\theta^{\prime}\right)=\sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\frac{\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)} \gamma^{t} \nabla_{\theta^{\prime}} \log \pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
$$


由于 $\theta$ 和 $\theta'$ 的差距非常小，所以在参数对应的位置可以进行整体性的替换得到


$$
\nabla_{\theta} \bar{A}(\theta)=\sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\frac{\pi_{\mathrm{g}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}{\pi_{\theta}\left(\mathbf{a}_{t} \mathbf{s}_{t}\right)} \gamma^{t} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]
\\
=\sum_{t} E_{\mathbf{s}_{t} \sim p_{\theta}\left(\mathbf{s}_{t}\right)}\left[E_{\mathbf{a}_{t} \sim \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)}\left[\gamma^{t} \nabla_{\theta} \log \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) A^{\pi_{\theta}}\left(\mathbf{s}_{t}, \mathbf{a}_{t}\right)\right]\right]=\nabla_{\theta} J(\theta)
$$


这个其实也就是 policy gradient 的梯度公式，除了约束的部分，整体和 policy gradient 没有差别了。


$$
\theta^{\prime} \leftarrow \arg \max _{\theta^{\prime}} \nabla_{\theta} J(\theta)^{T}\left(\theta^{\prime}-\theta\right)
$$


such that


$$
D_{\mathrm{KL}}\left(\pi_{\theta^{\prime}}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right) \| \pi_{\theta}\left(\mathbf{a}_{t} | \mathbf{s}_{t}\right)\right) \leq \epsilon
$$


这里我们希望将它当作一个正常的 policy gradient 进行操作，由于 Gradient ascent 的操作其实可以理解为在parameter space 上做一个小的修改，也就是说它相当于在 parameter space 上做约束的优化。

假设 parameter space 上的约束为


$$
\left\|\theta-\theta^{\prime}\right\|^{2} \leq \epsilon
$$


Gradient ascent 算法可以写为


$$
\theta^{\prime}=\theta+\sqrt{\frac{\epsilon}{\left\|\nabla_{\theta} J(\theta)\right\|^{2}}} \nabla_{\theta} J(\theta)
$$


我们重新从 policy iteration 回到了 policy gradient，所以可以认为 policy gradient 也就是相当于对 parameter space 做约束的 policy improvement 过程。

而它的问题也就来自这里，parameter space 上的约束和 policy space 上的约束是不同的，有可能 parameter space 只改变了一点点，就会导致 policy space 上巨大的改变，所以它的优化会比较不稳定。

基于这个问题，下面通过 natural gradient 来解决。



#### Natural Gradient





## 参考资料及致谢

所有参考资料在前言中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

