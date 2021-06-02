---
title: "REINFORCE算法"
collection: teaching
type: "强化学习教程-2"
permalink: /teaching/2021-RL-2
venue: "杜新宇,京东"
date: 2021-05-31
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1.策略梯度

**REINFORCE**算法是强化学习中常用的一个算法，属于策略梯度算法。强化学习的目标是，最大化累加的收益


$$
J(\pi_\theta)=\underset{\tau\sim\pi_\theta}{\mathbb{E}}[R(\tau)]\tag{1}
$$


其中$$\pi_\theta$$为策略函数，即agent采取的action通过该策略得到。策略函数一般用神经网络来拟合，$$\theta$$是策略函数的参数，也就是神经网络的参数，训练该神经网络模型就是调整$$\theta$$值，使得agent通过该策略与environment互动得到的累加收益最大化。

参数$$\theta$$的优化可通过梯度下降方法来实现：


$$
\theta_{k+1}=\theta_k+\alpha\nabla_\theta J(\pi_\theta)|_{\theta_k}\tag{2}
$$


上式中，未来累加收益的梯度$$\nabla_\theta J(\pi_\theta)$$称为**策略梯度**。策略梯度类算法的目的就是找到一组参数$$\theta$$，使得策略梯度为0，此时未来累加收益$$J(\pi_\theta)$$取得最大值。



要想让计算机通过上述算法训练模型，需要两方面的准备工作。1.需要导出**策略梯度**$$\nabla_\theta J(\pi_\theta)$$的具体公式，并且该公式应该是收益期望值的形式$$\mathbb{E}[R(\tau)]$$。2.通过收集agent与environment互动的数据，计算出该期望值$$\mathbb{E}[R(\tau)]$$。

 

## 2.数学推导

2.1 迹和迹的概率

**迹**用数学符号$$\tau$$表示，公式(1)等号右侧出现过。**迹**代表一系列的状态和行动$$\tau=(s_0,a_0,...,s_T,a_T,s_{T+1})$$。**迹的概率**可表示为：


$$
P(\tau |\theta)=\rho_0(s_0)\prod_{t=0}^{T}P(s_{t+1} |s_t,a_t)\pi_\theta(a_t |s_t)\tag{3}
$$


2.2 对数函数的梯度


$$
\nabla_\theta logP(\tau |\theta)=\frac{\nabla_\theta P(\tau |\theta)}{P(\tau | \theta)}\\
\nabla_\theta P(\tau |\theta)=P(\tau |\theta)\nabla_\theta logP(\tau |\theta)\tag{4}
$$


3.3 迹的概率取对数


$$
logP(\tau |\theta)=log\rho_0(s_0)+\sum_{t=0}^T(logP(s_{t+1} |s_t,a_t)+log\pi_\theta(a_t |s_t))\tag{5}
$$




对式(5)求梯度可得：


$$
\nabla_\theta logP(\tau |\theta) = \nabla_\theta log\rho_0(s_0)+\sum_{t=0}^T(\nabla_\theta logP(s_{t+1} |s_t,a_t)+\nabla_\theta log\pi_\theta(a_t |s_t)) \\
因为 \rho_0(s_0), P(s_{t+1} |s_t,a_t)和R(\tau)都和\theta无关，因此上式可简化为\\
\nabla_\theta logP(\tau |\theta)=\sum_{t=0}^T\nabla_\theta log\pi_\theta(a_t |s_t))\tag{6}
$$


3.4 **策略梯度**

对式(1)求梯度得：


$$
\nabla_\theta J(\pi_\theta)=\nabla_\theta\underset{\tau\sim\pi_\theta}{\mathbb{E}}[R(\tau)] \\
=\nabla_\theta \int_\tau P(\tau |\theta)R(\tau) \#期望值的定义 \\
=\int_\tau\nabla_\theta P(\tau |\theta)R(\tau) \#梯度符号拿到积分内 \\
=\int_\tau P(\tau |\theta)\nabla_\theta logP(\tau |\theta)R(\tau) \#将式(4)带入 \\
最终将式(6)带入得到策略梯度：
$$


$$
\nabla_\theta J(\pi_\theta)=\underset{\tau\sim\pi_\theta}{\mathbb{E}}[\sum_{t=0}^T\nabla_\theta log\pi_\theta(a_t |s_t)R(\tau)]\tag{7}
$$


式(7)等号右侧可以通过收集agent和environment的互动数据计算得出。例如，我们可以让agent与environment按照策略$$\pi_\theta$$互动，收集各种迹的集合$$\mathcal{D}=\{\tau_i\}_{i=1,...,N}$$，那么**策略梯度**可以利用如下公式计算：


$$
\hat{g}=\frac{1}{\mid\mathcal{D}\mid}\sum_{\tau\in\mathcal{D}} \sum_{t=0}^{T}\nabla_\theta log\pi_\theta(a_t |s_t)R(\tau)\tag{8}
$$

其中：$$\mid\mathcal{D}\mid$$是我们收集的迹集合中**迹的数量**。$$\pi_\theta$$是一个以$$\theta$$为参数的神经网络模型，$$R(\tau)$$是agent与environment互动中获得的累加收益。因此，式(8)完全可以用计算机计算得出。计算得出的数值带入式(2)即可完成模型的训练。



## 3.伪代码

1.给策略模型的参数$$\theta$$随机赋值初始化。

2.用初始化的策略模型$$\pi_\theta$$产生一个迹：$$S_1,A_1,R_2,S_2,A_2,...,S_T$$。

3.For t=1,2,...,T:

&emsp;1.估计回报$$G_t$$

&emsp;2.更新策略模型参数：$$\theta\leftarrow\theta+\alpha\gamma^2G_t\nabla_\theta log\pi_\theta(A_t\mid S_t)$$

通常会在$$G_t$$中减去一个基础值，从而使得梯度数据分布的方差降低的同时保持偏差不变。普遍的做法是从动作价值中减去状态价值，称为优势值advantage：$$A(s,a)=Q(s,a)-V(s)$$。因此，常常使用优势值A来更新策略模型的参数。