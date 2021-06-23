---
title: "Actor-Critic算法"
collection: teaching
type: "强化学习教程-3"
permalink: /teaching/2021-RL-3
venue: "杜新宇,京东"
date: 2021-06-02
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

## 1.Actor-Critic

REINFORCE算法中需要价值$$G_t$$来更新策略模型的参数$$\theta$$，然而有些情况下，价值并不能显性的得到。因此，建立另一个模型来近似价值函数是非常有必要的。**Actor-Critic**算法的思路就是建立两个模型，一个策略模型，一个价值模型。分别称为Actor和Critic，翻译为演员和影评人。

&emsp;**Critic**：参数为$$w$$的价值模型，可以是动作价值函数$$Q_w(a\mid s)$$或者状态价值函数$$V_w(s)$$。负责用来评价策略的好坏。

&emsp;**Actor**：参数为$$\theta$$的策略模型。负责输出动作。

## 2.算法逻辑

1.随机初始化状态、策略模型参数、和价值模型参数$$s,\theta,w$$。根据策略模型采样动作$$a\sim\pi_\theta(a\mid s)$$

2.For t=1,2,...,T:

&emsp;1.采样t步的收益$$r_t\sim R(s,a)$$和下一个状态$$s'\sim P(s'\mid s,a)$$

&emsp;2.采样下一个动作$$a'\sim\pi_\theta(a'\mid s')$$

&emsp;3.更新策略模型参数$$\theta \leftarrow \theta+\alpha_\theta Q_w(s,a)\nabla_\theta log\pi_\theta(a\mid s)$$

&emsp;4.计算价值的时间差分$$\delta_t=r_t+\gamma Q_w(s',a')-Q_w(s,a)$$，并用它更新价值模型的参数：$$w\leftarrow w+\alpha_w \delta_t\nabla_wQ_w(s,a)$$

&emsp;5.更新动作和状态$$a\leftarrow a',s\leftarrow s'$$

其中$$\alpha_\theta, \alpha_w$$称为学习速率，即梯度下降中的步长。