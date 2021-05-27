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

其中$$\mathcal{s}$$为所有可能状态的集合，$$\mathcal{A}$$为所有可能行动或决策的集合，$$\mathcal{R}$$是收益函数，$$\mathcal{P}$$是状态转移概率，$$\mathcal{\gamma}$$是折扣因子，用来对远期收益进行折扣，即在计算收益时近期的收益占比重较大，越远期比重越小。

# 参考文献：

[1] https://arxiv.org/abs/2101.06286