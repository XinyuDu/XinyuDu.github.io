---
title: "强化学习问题的框架"
collection: teaching
type: "强化学习教程-1"
permalink: /teaching/2021-RL-1
venue: "杜新宇,京东"
date: 2021-05-27
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1.强化学习问题的框架

​		强化学习问题的常用框架是**智能体-环境(agent-environment)**模型，如下图<sup>[1]</sup>所示。

<div align="center"><img src = "./2021-RL/agent-environments.jpg"/></div>

其中，**agent**负责学习和做决策，除agent以外的都称为**environment**，上图中的Environment和Interpreter其实都属于**environment**。在某个时刻***t***，agent通过观察环境的状态***state<sub>t</sub>***，做出决策，采取行动***action<sub>t</sub>***，从环境获得收益***reward<sub>t</sub>***并且通过观察得到一个新的状态***state<sub>t+1</sub>***。类比下棋可以很好的理解这个过程。

​		agent做决策的过程是典型的Markov decision process (**MDP**)，可以用下述五元组的形式描述：
$$
(\mathcal{S,A,R,P,\gamma})
$$

其中$$\mathcal{S}$$为所有可能状态的集合，$$\mathcal{A}$$为所有可能行动或决策的集合，$$\mathcal{R}$$是收益函数，$$\mathcal{P}$$是状态转移概率，$$\mathcal{\gamma}$$是折扣因子，用来对远期收益进行折扣，即在计算收益时近期的收益占比重较大，越远期比重越小。

​		agent的目标就是学习一个策略$$\pi(a\vert s)$$，使得在状态$$s\in\mathcal{S}$$的情况下采取行动$$a\in\mathcal{A}$$，使得未来的累加收益总和的期望值最大化。


$$
max \mathbb{E}[R(\mathcal{\tau})]\\
其中，\mathcal{R(\tau)}=\sum_{t=0}^\tau\gamma^tr(a_t,s_t)\\
0\le\gamma\le1
$$

​		强化学习的主要目的是学习一个策略$$\pi(a\vert s)$$, 使得累加收益期望最大化，该策略的数学形式为条件概率，即在状态s的情况下，采取动作a的概率。当算法在学习过程中采用相同的策略评价和改进时称为on-policy算法。如果产生数据的策略和被评估和改进的策略不相同，则该算法称为off-policy算法。

# 2.数学基础

2.1 期望值$$\mathbb{E}$$的定义为：


$$
\mathbb{E}[f(x)] = \sum_xP(x)f(x)\\
其中P(x)为变量x的概率函数，f(x)为自变量为x的函数。
$$

假如，x代表某单位员工的身高，f(x)为身高体重函数，P(x)为身高的概率函数，那么f(x)的期望值即为该单位员工的平均体重。

2.2 条件概率：

条件概率是指，在事件B发生的条件下，A事件发生的概率。条件概率表示为：


$$
P(A\vert B)=\frac{P(AB)}{P(B)} \\
P(AB)为事件A和B同时发生的概率\\
P(B)为事件B发生的概率
$$


# 参考文献：

[1] https://arxiv.org/abs/2101.06286