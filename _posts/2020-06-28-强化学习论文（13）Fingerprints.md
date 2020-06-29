---

layout:     post
title:      "强化学习论文（13）Fingerprints"
subtitle:   "Stabilising Experience Replay for Deep Multi-Agent Reinforcement Learning"
date:       2020-06-28 10:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
mathjax: true
tags:
    - 强化学习
    - 强化学习论文
---



**标签：**

Fingerprints; value-based; off-policy; model-free; discrete action space; continuous state space; cooperative task; decentralized approach;



[论文链接](https://arxiv.org/abs/1702.08887v3)



## 创新点及贡献

1、针对 IQL 中的非平稳性导致的经验回放技术无法使用的问题，提出了两种解决方案：第一种是基于 off-environment 的 Importance sampling 用于自然衰减过时的数据，第二种是将 fingerprints 作为每个智能体的价值函数的条件，从而消除采样数据的年龄问题。



## 研究痛点

1、深度强化学习严重依赖于经验回放技术，但是 IQL 的非平稳性使得经验池中的数据无法再反映当前正在学习的动态（参考 MADDPG 有理论证明）

2、目前的方法通常要么只使用近期的经验进行训练，要么直接不使用经验回放技术，这些变通方法都限制住了样本效率并影响了训练的稳定性。



## 算法流程

本文提出了两种方法使得我们在 IQL 下可以使用稳定的经验回放技术，分别是 Importance sampling 和 fingerprints



### Importance Sampling

首先考虑 fully- observable multi-agent setting，然后考虑 partially observable multi-agent setting

#### fully- observable multi-agent setting

1、首先在多智能体环境下某个智能体的动作价值函数可以写成下式

- 注意到其中的 $\pi_{-a}(\mathbf{u}_{-a} \mid s)$ 便是非平稳部分，它会随着其他智能体的策略改变而改变。

<img width="70%" src="/img/in-post/2020-06-28-强化学习论文（13）Fingerprints.assets/image-20200628215031656.png"/>



2、因此我们在每个时刻存储经验到经验池中的时候，加多一项额外项 $\pi_{-a}(\mathbf{u}_{-a} \mid s)$，即每个时刻 $t_c$ 存储如下五元组

<img width="30%" src="/img/in-post/2020-06-28-强化学习论文（13）Fingerprints.assets/image-20200628215428817.png"/>



3、在时刻 $t_r$ 进行采样训练的时候，我们进行基于 off-environment 的 Importance sampling。

<img width="70%" src="/img/in-post/2020-06-28-强化学习论文（13）Fingerprints.assets/image-20200628215527127.png"/>



#### partially observable multi-agent setting

1、考虑一个 augmented game 定义如下

<img width="70%" src="/img/in-post/2020-06-28-强化学习论文（13）Fingerprints.assets/image-20200628220024398.png"/>



2、其贝尔曼方程可以写为

- 其中将式（5）代入式（6）得到式（7）

<img width="70%" src="/img/in-post/2020-06-28-强化学习论文（13）Fingerprints.assets/image-20200628220121133.png"/>



3、可以看到在部分可观察环境中 $\pi_{-a}(\mathbf{u}_{-a} \mid s)$ 依旧是导致非平稳性的原因，但与完全可观察不同，还有其他项也会导致非平稳，因此这里使用上述的重要性采样只是一种估计。



### Fingerprints

1、在 IQL 中如果将其他智能体的策略作为其动作价值函数的条件，那么动作价值函数就可以稳定。

- 在 hyper Q-learning 中提出一种思想是将每个智能体的状态空间用通过贝叶斯推断的其他智能体的策略进行增广，即 $O^{\prime}(s)=\{O(s), \theta_{-a}\}$。
- 但该方法在其他智能体也采用神经网络时，会导致输入空间的参数过多。



2、一个重要的点是我们并不需要以任何可能的 $\theta_{-a}$ 为条件，而仅需要实际出现在经验池中的 $\theta_{-a}$ 的值，而我们可以将经验池中的生成数据的策略参数从一个高维策略空间映射到一个一维的轨迹上，就是说我们只需要为每个智能体的观察结果消除歧义，使得我们在训练时知道某个样本来自于何时即可。



3、所以文中采用了 fingerprint 的方法替代以上方案，即 $O^{\prime} = \{O(s), \epsilon, e\}$

- $\epsilon$ is the rate of exploration
- $e$ is the training iteration number



## 实验

1、在 Decentralised StarCraft Micromanagement 中进行实验。



2、实验表明 IS 和 FP 在全连接神经网络中效果更明显，而在 DRQN 中没有太大的提升。



3、将 IS 和 FP 一起使用与单独使用其中一个没有太大的提升效果。



## 其他补充

1、关于部分可观察环境中采用 augmented game 来证明的操作看不太懂。

2、论文的方法感觉挺简单的，实验效果也没有特别好，可能是因为 idea 比较新颖，解决了经验回放的平稳性问题而发表了吧。



## 参考资料及致谢

所有参考资料在《强化学习思考（1）前言》中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

