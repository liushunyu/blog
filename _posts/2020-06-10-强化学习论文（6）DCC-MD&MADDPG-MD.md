---

layout:     post
title:      "强化学习论文（6）DCC-MD&MADDPG-MD"
subtitle:   "Message-Dropout: An Efficient Training Method for Multi-Agent Deep Reinforcement Learning"
date:       2020-06-10 10:00:00
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

DCC-MD; value-based; discrete action space; 

MADDPG-MD; actor-critic; continuous action space; 

off-policy; model-free; communication; continuous communication channel; continuous state space; cooperative task;



[论文链接](https://arxiv.org/abs/1902.06527)



## 创新点及贡献

1、基于 Dropout 的思想提出了一种 Message-Dropout 的方法，并将其应用在 decentralized control with communication (DCC) 算法和 MADDPG 算法上。

2、对于 DCC 来说，Message-Dropout 主要体现在 Q 函数中对其他智能体发来的通信信息进行随机 dropout；对于 MADDPG 来说，Message-Dropout 主要体现在 Q 函数中对其他智能体的观察进行随机 dropout。



## 研究痛点

1、随着多智能体数量的增加，DCC 算法和 MADDPG 算法的输入空间都线性地增长，因此学习所需要的样本数量也会随之增加，从而大大降低学习速度。



## 算法流程

分别介绍 DCC-MD 和 MADDPG-MD



### DCC-MD

DCC-MD 框架图如下

<img width="100%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610191641446.png"/>



1、simple DCC：基于 Double DQN，在每个智能体的 Q 函数的输入中加入其他智能体对该智能体发来的通信信息。

- 定义智能体 $i$ 接收到的信息 $m^i = (m^{i,1},...,m^{i,i-1},m^{i,i+1},...,m^{i,N})$，其中 $m^{i,j}$ 为智能体 $i$ 从智能体 $j$ 接收到的信息。
- 损失函数定义如下：

<img width="80%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610190517416.png"/>



2、针对 simple DCC 算法的输入空间都线性地增长，从而大大降低学习速度的缺点，提出了 Message-Dropout 方法。

- 在训练阶段基于概率 $p$ 对智能体 $i$ 接收到的信息 $m^i$ 进行 block-wise 的 dropout，即每个 $m^{i,j}$ 都有概率 $p$ 被置为零向量。
- 在测试阶段不再 dropout，但为了保证输出正确的动作，需要将 $m^i$ 乘以权重 $(1-p)$。
- 注意 Message-Dropout 不应用于智能体 $i$ 的观察 $o^i$ 上。



3、基于 Message-Dropout 的 DCC-MD

- 损失函数定义如下，注意作用于 $m_{j}$ 和 $m_{j+1}$ 的 $b_{j,k}$  需要保持一致

<img width="80%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610192114524.png"/>

<img width="80%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610192143125.png"/>

<img width="20%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610192210603.png"/>

- 举个三个智能体的例子，其 Q 网络的输入共有四种可能

<img width="70%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610192316847.png"/>



4、simple DCC 算法中由于其他智能体通信信息占用输入空间过大，导致智能体自身的观察的重要性有可能被忽略，针对这个问题可以从网络结构中采用分支输入来解决。



#### 算法伪代码

DCC-MD 算法伪代码如下

<img width="80%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610185545591.png"/>



### MADDPG-MD

MADDPG-MD 框架图如下

<img width="100%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610191711634.png"/>



1、针对 MADDPG 算法的输入空间都线性地增长，从而大大降低学习速度的缺点，提出了 Message-Dropout 方法。

- 在训练阶段基于概率 $p$ 对智能体 $i$ 的 Q 函数的其他多智能体的观察 $o^{-i}$ 进行 block-wise 的 dropout，即每个 $o^{j}$ 都有概率 $p$ 被置为零向量。
- 注意 Message-Dropout 不应用于智能体 $i$ 的观察 $o^i$ 上。



2、损失函数的定义如下，注意作用于 $x$ 和 $x'$ 的 $b_{j}$  需要保持一致。

<img width="70%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610193038012.png"/>



3、MADDPG 算法中由于其他智能体通信信息占用输入空间过大，导致智能体自身的观察的重要性有可能被忽略，针对这个问题可以从网络结构中采用分支输入来解决。



## 实验

1、采用了三个实验环境，在实验中将智能体的观察作为通信信息。

<img width="80%" src="/img/in-post/2020-06-10-强化学习论文（6）DCC-MD&MADDPG-MD.assets/image-20200610191823279.png"/>



2、Ablation Studies

- Dropout rate：0.2 至 0.5 较好
- Block-wise dropout versus element-wise dropout：基于 Block-wise 的意思是整个 $m^{i,j}$ 向量作为一个整体进行 dropout，element-wise 则是 $m^{i,j}$ 向量中的元素自己作为一个整体进行 dropout。
- Retaining agent’s own observation without dropout
- Model architecture



3、Test in The Unstable Environment：随机关闭半数或所有的通信信道观察算法的表现

- 当只关闭半数时 DCC-MD 优于 DCC 和 FDC：证明 DCC-MD 的鲁棒性较好。
- 当全部关闭时 DCC-MD 优于 DCC，但劣于 FDC：证明当不稳定性过大时 DCC-MD 无法恢复通信损失，但 FDC 仍可以表现良好。



## 其他补充

1、本人认为本文没有直接去改变 DCC 或者 MADDPG 的基本框架中输入空间的线性增长，而是通过 dropout 技术将这种线性增长带来的学习效率降低的问题进行解决，这种曲线救国的思路还是很值得我们借鉴的，其实验中采用的智能体也相对较多，可以达到十个左右。



## 参考资料及致谢

所有参考资料在《强化学习思考（1）前言》中已列出，再次向强化学习的大佬前辈们表达衷心的感谢！

