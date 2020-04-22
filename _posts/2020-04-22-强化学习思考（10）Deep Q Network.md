---
layout:     post
title:      "强化学习思考（10）Deep Q Network"
subtitle:    "Deep Q Network"
date:       2020-04-22 10:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
mathjax: true
tags:
    - 强化学习
    - 强化学习思考
---



关于 Deep Q Network 的注意事项。



## 目录

- [强化学习思考（1）前言](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-1-%E5%89%8D%E8%A8%80/)
- [强化学习思考（2）强化学习简介](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-2-%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E7%AE%80%E4%BB%8B/)
- [强化学习思考（3）马尔可夫决策过程](https://liushunyu.github.io/2020/04/12/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-3-%E9%A9%AC%E5%B0%94%E5%8F%AF%E5%A4%AB%E5%86%B3%E7%AD%96%E8%BF%87%E7%A8%8B/)
- [强化学习思考（4）模仿学习和监督学习](https://liushunyu.github.io/2020/04/13/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-4-%E6%A8%A1%E4%BB%BF%E5%AD%A6%E4%B9%A0%E5%92%8C%E7%9B%91%E7%9D%A3%E5%AD%A6%E4%B9%A0/)
- [强化学习思考（5）动态规划](https://liushunyu.github.io/2020/04/15/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-5-%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92/)
- [强化学习思考（6）蒙特卡罗和时序差分](https://liushunyu.github.io/2020/04/15/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-6-%E8%92%99%E7%89%B9%E5%8D%A1%E7%BD%97%E5%92%8C%E6%97%B6%E5%BA%8F%E5%B7%AE%E5%88%86/)
- [强化学习思考（7）策略梯度](https://liushunyu.github.io/2020/04/18/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-7-%E7%AD%96%E7%95%A5%E6%A2%AF%E5%BA%A6/)
- [强化学习思考（8）Actor-Critic 方法](https://liushunyu.github.io/2020/04/21/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-8-Actor-Critic-%E6%96%B9%E6%B3%95/)
- 强化学习思考（9）值函数方法
- 强化学习思考（10）Deep Q Network



## Deep Q-learning

### Q-learning

- sequential states are strongly correlated
- target value is always changing



### replay buffers

- samples are no longer correlated
- multiple samples in the batch (low-variance gradient)



###  target network

- targets don’t change until N steps
- update target network
  - $\phi' \leftarrow \phi$
  - Polyak averaging ($\tau=0.999$ works well)


$$
\phi' \leftarrow \tau\phi'+(1-\tau)\phi
$$


### Deep Q-learning 算法

- 注意 Q network 初始化权重需要比较小，这样网络前期训练可以更依赖于 target 中的 $r$ 值

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819222132744.png"/>



## 算法扩展

### Double DQN

1、为什么 $Q$ 值经常被高估？

- 因为目标值 $r_t+maxQ(s_{t+1}, a)$ 总是倾向于选择被高估的行动 action。
- 考虑随机变量 $Q$，这些变量都有一些噪音，而取 $\max$ 操作则扩大化了这些噪音，在这种情况下我们是关于噪音取 $\max$，可能我们找到的是若干个数中噪音最大的那个。


$$
E\left[\max \left(Q_{1}, Q_{2}\right)\right] \geq \max \left(E\left[Q_{1}\right], E\left[Q_{2}\right]\right)
$$


- 我们的 $Q$ 都是从样本轨迹里学出来的，所以都含噪音，因此我们可能得到的只是噪音最高的选项而不是真正 $Q$ 最大的选项。



2、double DQN 是如何工作的？

- 使用两个 Q-function（因此称为 double）, 一个用来选择行动 action，另外一个用来计算 $Q$ 值，通常会选择 target network 来作为另外一个用于计算 $Q$ 值的 Q‘-function。

- 如果 $Q$ 高估了 $a$ 从而被选择， $Q’$ 会给这个被选择的 $a$ 一个合适的 $Q$ 值；如果 $Q’$ 会高估某个 action $a$，这个 action 并不会被 $Q$ 选择到。


$$
Q\left(s_{t}, a_{t}\right) \longleftrightarrow r_{t}+Q^{\prime}\left(s_{t+1}, a r g \max _{a} Q\left(s_{t+1}, a\right)\right)
$$



### Dueling DQN

这种类型的网络结构可以用来学习不被行动 action 影响下的 state 的价值，如果模型更倾向于去改变 $V(s)$ 的值，那每一次 $V(s)$ 都会同时更新当前 $s$ 下的所有 $Q(s,a)$，效率大大提升。

为了不让模型学习到 $Q(s,a)=A(s,a)$，我们对 $A(s,a)$  减去其均值，使 $A(s,a)$ 具有零和特征，这时候学习到的 $V(s)$ 便相当于 $Q(s,a)$ 的平均值。

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819223356959.png"/>



### Prioritized Experience Replay

在训练的过程中，对于在经验 buffer 里面的样本，那些具有更大的 TD 误差的样本会有更高的概率被采样，这样可以加快训练速度，此外在这个过程中，参数更新的过程也会有相应的更改。

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819224532388.png"/>





### Multi-step: Combination of MC and TD

模型需要学习多步累积起来的回报 reward，也就是说将 MC 和 TD 进行了折中，同时引入了一个超参数，即累积 reward 的步长 $N$。

- less biased target values when Q-values are inaccurate
- typically faster learning, especially early on
- only actually correct when learning on-policy

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819224640633.png"/>

如何解决 on-policy 到 off-policy 的使用：

- ignore the problem
  - often works very well
- cut the trace – dynamically choose N to get only on-policy data
  - works well when data mostly on-policy, and action space is small
- importance sampling

For more details, see: “Safe and efficient off-policy reinforcement learning.” Munos et al. ‘16



### Noisy Net

**Epsilon Greedy: 在行动上加噪声**
$$
a=\left\{\begin{array}{cc}{\arg \max _{a} Q(s, a),} & {\text { with probability } 1-\varepsilon} \\ {\text { random, }} & {\text { otherwise }}\end{array}\right.
$$
即便给定相同的状态 state，agent 也有可能采取不同的行动，因此不符合实际上意义上的 policy。



**Noisy Net: 在参数上加噪声**

在每个 episode 开始时，在 Q-function 的参数上引入噪声，但在每一个 episode 内，参数不会发生改变，所以给定同样的 state，agent 会采取同一个 action，这保证了探索的相对稳定性。
$$
a=\arg \max _{a} \tilde{Q}(s, a)
$$



### Distributional Q-function

状态-行动价值函数 $Q^\pi(s,a)$ 是累积收益的期望值，也就是说是价值分布的均值。然而有的时候不同的分布得到的均值可能一样，但我们并不知道实际的分布是什么。

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819225321134.png"/>

Distributional Q-function 认为可以输出Q值的分布，当具有相同的均值时，选择具有较小方差（风险）的那一个，但实际上这个方法很难付诸实践。



### Rainbow

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819225419121.png"/>

上述图像表明 DDQN 对于 rainbow 来说用处不大，这是因为 DDQN 是用来解决高估问题的，而这个问题在 distributional Q-function 中已经得到了解决。



## Q-learning with continuous actions

对于连续的 $a$ 来说要解出下面的式子是比较难的。 


$$
a=\arg \max_a Q(s,a)
$$


### Option 1: optimization

- gradient based optimization (e.g., SGD)
  - a bit slow in the inner loop
- stochastic optimization when action space typically low-dimensional
  -  dead simple and efficiently parallelizable, but not very accurate
- cross-entropy method (CEM)
  - simple iterative stochastic optimization
- CMA-ES
  - substantially less simple iterative stochastic optimization



these method works OK, for up to about 40 dimensions



### Option 2: use function class that is easy to optimize

设计一个网络来使得这个优化过程更简单，譬如假设 $Q$ 函数是二次函数

- NAF: Normalized Advantage Functions
  - no change to algorithm
  - just as efficient as Q-learning
  - loses representational power: 缺点就在于 Q 函数只能是固定的形式（如这里的二次函数），非常受限，Q 函数的建模泛化能力将大大降低。

<img width="480" src="/img/in-post/2020-04-22-强化学习思考（10）Deep Q Network.assets/image-20190819225620305.png"/>

这里 $\sum$ 和 $\mu$ 是高斯分布的方差和均值，因此，该矩阵 $\sum$ 一定是正定的。要让 $Q$ 值较高，意味着要使得 $(a-\mu)^2$ 的值更小，也就是说 $a=\mu$。


$$
\mu(s) = \arg \max_a Q(s,a)\\
V(s) = \max_a Q(s,a)
$$




### Option 3: learn an approximate maximizer

- DDPG
  - “deterministic” actor-critic
  - 参考[强化学习思考（8）Actor-Critic 方法](https://liushunyu.github.io/2020/04/21/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0%E6%80%9D%E8%80%83-8-Actor-Critic-%E6%96%B9%E6%B3%95/)中的 Pathwise Derivative Policy Gradient



## tips for Q-learning

1、对于不同的问题，Q-learning 在有些问题上很可靠，在有些问题上波动很大，需要花很多力气来让 Q-learning 稳定下来。因此发现几个能让 Q-learning 比较可靠的问题来试验程序，譬如 Pong 和 Breakout。如果这些例子上表现不好，那就说明程序有问题。

2、回放缓冲池的大小越大，Q-learning 的稳定性越好。我们往往会用到上百万个回放样本，那么内存上怎么处理是决定性的。建议图像使用uint8 (1字节无符号整型) 存储，然后在存储 $(s,a,r,s')$ 的时候不要重复存储同样的数据。

3、训练的时候要耐心。DQN 的收敛速度很慢，对于 Atari 游戏经常需要 1000-4000 万帧，用 GPU 也得几个小时到一天的时间，这样才能看出能显著地比随机策略要来的好。

4、在使用 $\epsilon$ 贪心等策略的时候，一开始把探索率调高一些，然后逐渐下降。

5、Bellman 误差可能会非常大，因此可以对梯度进行裁剪（clipping，也就是设一个上下限），或者使用Huber损失进行光滑。


$$
L(x)=\left\{\begin{array}{ll}
x^{2} / 2 & \text { if }|x| \leq \delta \\
\delta|x|-\delta^{2} / 2 & \text { otherwise }
\end{array}\right.
$$


6、在实践中，使用 Double Q-learning 很有帮助，改程序也很简单，而且几乎没有任何坏处。

7、使用 N 步收益也很有帮助，但是可能会带来一些问题。

8、除了探索率外，学习率 (Learning Rate, 也就是步长) 也很重要，可以在一开始的时候把步长调大一点，然后逐渐降低，也可以使用诸如 ADAM 的自适应步长方案。

9、多用几个随机种子试一试，有时候表现差异会很大。



## 参考资料及致谢

所有参考资料在前言中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

