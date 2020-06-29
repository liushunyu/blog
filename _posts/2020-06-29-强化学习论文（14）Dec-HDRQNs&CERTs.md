---

layout:     post
title:      "强化学习论文（13）Dec-HDRQNs&CERTs"
subtitle:   "Deep Decentralized Multi-task Multi-Agent Reinforcement Learning under Partial Observability"
date:       2020-06-29 09:00:00
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

Dec-HDRQNs; CERTs; value-based; off-policy; model-free; discrete action space; continuous state space; cooperative task; decentralized approach; multi-agent; multi-task; transfer learning; distillation;



[论文链接](https://arxiv.org/abs/1703.06182)



## 创新点及贡献

1、论文主要关注点在于 decentralized MT-MARL under partial observability，并提出了一个两阶段的 MT-MARL 方法来解决该问题。



2、第一个阶段主要是 single-task，分为两步，首先是利用了 Hysteretic Q-learning 谨慎乐观的思想与 DRQN 进行结合得到 Dec-HDRQNs 算法用于价值函数估计，然后采用了 Concurrent Experience Replay Trajectories (CERTs) 优化经验回放技术，使其训练更加稳定。



3、第二个阶段主要是 multi-tasks，首先利用第一个阶段的方法针对每个 single-task 进行训练，然后将所有 single-task 上的知识蒸馏到一个到一组处理  multi-tasks 的智能体上。



## 研究痛点

1、decentralized MT-MARL under partial observability 具有较大的非平稳性。



## 算法流程

论文算法分为两个阶段，第一阶段主要是 single-task，第二阶段将其拓展到 multi-tasks



### Phase I: Dec-POMDP Single-Task MARL As

主要使用了 Dec-HDRQNs 和 CERTs 两种技术

#### Dec-HDRQNs

1、利用 Hysteretic Q-learning 的思想与 DRQN 进行结合得到 Dec-HDRQNs 算法。



2、Hysteretic Q-learning 使用了两种学习率如下，这种方法可以使得智能体对因队友的探索导致的负学习现象更为鲁棒。

- 当 TD error 是非负的时候使用 nominal learning rate $\alpha$ 
- 当 TD error 是负的时候使用 smaller learning rate $\beta$ 
- 其中 $0 < \beta < \alpha < 1$



#### CERTs

1、针对 DRQN 的训练提出了 concurrent experience replay trajectories 的概念，即每个智能体在独立训练自己的 Q-function 时，从经验池中采样出来的数据需要从 episode 层面以及时间步层面上对齐。

<img width="80%" src="/img/in-post/2020-06-29-强化学习论文（14）Dec-HDRQNs&CERTs.assets/image-20200629114443632.png"/>



### Phase II: Dec-POMDP MT-MARL

1、将上述的 Dec-HDRQNs 和 CERTs 方法结合蒸馏技术运用在 multi-tasks 环境下



2、首先利用 Dec-HDRQNs 为每一个 single-task 进行训练并将数据集存储在 CERTs 中。

<img width="80%" src="/img/in-post/2020-06-29-强化学习论文（14）Dec-HDRQNs&CERTs.assets/image-20200629114959751.png"/>



3、然后采用蒸馏的方法将所有 single-task 上的知识蒸馏到一个到一组处理  multi-tasks 的智能体上。

<img width="80%" src="/img/in-post/2020-06-29-强化学习论文（14）Dec-HDRQNs&CERTs.assets/image-20200629115023087.png"/>



## 实验

1、在 variations of the existing meeting-in-a-grid Dec-POMDP benchmark 中进行实验。

- 根据 grid-world 的 $m$ 的大小定义为不同的 task

<img width="100%" src="/img/in-post/2020-06-29-强化学习论文（14）Dec-HDRQNs&CERTs.assets/image-20200629115419034.png"/>



## 其他补充

1、CERTs 各轴上的对齐用于多智能体 DRQN 的训练中应该是挺理所当然的且必须的。

2、直接将 Q 值进行 softmax 蒸馏感觉有一点点奇怪，怎么保证学生网络的 Q 值的量纲与教师网络一样呢？然后突然想到 Q 网络最后加多一层 softmax 层貌似就可以变成策略网络了。



## 参考资料及致谢

所有参考资料在《强化学习思考（1）前言》中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

