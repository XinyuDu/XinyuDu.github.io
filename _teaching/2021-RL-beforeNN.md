---
title: "神经网络出现前的强化学习"
collection: teaching
type: "强化学习教程-6"
permalink: /teaching/2021-RL-6
venue: "杜新宇,京东"
date: 2022-03-08
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

## 1.卖拐ENV

首先，我们根据小品卖拐的故事，构建一个超级简单的环境（ENV），该ENV可以根据action产生state和reward。这个ENV只有两个状态和两个动作，之所以称之为超级简单就是因为其动作空间和状态空间都很小。两个状态分别为：$$S_1$$相遇，$$S_2$$卖拐。两个动作分别为：$$A_1$$攀谈，$$$A_2$$离开。上述ENV中，范伟就是agent。他可以根据不同的state，采取不同的action。在**相遇**这个状态时，范伟有两个选择，离开或者攀谈。如果选择***攀谈***这个动作，不会产生任何后果，因此ENV会给出reward=0，如果选择***离开***这个动作，大忽悠会说你看他腿有病，给范伟造成心灵伤害，因此ENV会给出reward=-10。在**卖拐**这个状态时，范伟也有两个选择，***攀谈***和***离开***。如果继续***攀谈***就会被忽悠住，因此ENV给出reward=-100。如果***离开***，则没被忽悠住，ENV给出reward=100。

|                                                              | <img src="./2021-RL/talk.png" style="zoom: 33%;" />$$A_1$$=攀谈 | <img src="./2021-RL/run.png" style="zoom:33%;" />$$A_2$$=离开 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img src="./2021-RL/meet.png" style="zoom:25%;" /> $$S_1$$=相遇 | $$Q(S_1,A_1)$$                                               | $$Q(S_1,A_2)$$                                               |
| <img src="./2021-RL/buy.png" style="zoom:25%;" />$$S_2$$=卖拐 | $$Q(S_2,A_1)$$                                               | $$Q(S_2,A_2)$$                                               |



## 2.基本概念

$$
\begin{array}{l}
Reward \quad R_t, \quad agent给出动作A_{t-1}后，ENV返回给agent的奖励或收益。\\如上ENV中，在相遇这个状态(S_1)范伟给出动作离开(A_2)获得的奖励为R_2=-10.
\end{array}
$$
---

$$
\begin{array}{l}
Return \quad G_t=\sum_{k=0}^\infty\gamma^kR_{t+k+1}, \quad 从t时刻到结束状态累加的奖励。\\\gamma\in(0,1)为累加因子，离当前时间越远的奖励，占比越小。\\比如在状态相遇时范伟采取攀谈这个动作，在状态卖拐时，范伟采取离开这个动作，\\那么G_1=\gamma*R_2+\gamma^2*R_3=\gamma*0+\gamma^2*100=100\gamma^2
\end{array}
$$
---

$$
\begin{array}{l}
State\quad Value \quad V(S_t)=\mathbb{E}[G_t|S_t=s]=\mathbb{E}[\sum_{k=0}^\infty\gamma^kR_{t+k+1}|S_t=s],\\状态s的价值函数，用累加收益的期望来刻画。\\如上所示，在状态S_t的累加收益G_t存在多种取值，比如在状态相遇时范伟采取攀谈这个动作，\\但在状态卖拐时，范伟不采取离开这个动作而是继续攀谈。那么G_1=\gamma*0+\gamma^2*(-100)=-100\gamma^2\\因此，需要用累加收益的期望值来刻画状态价值V(s)
\end{array}
$$
---

$$
\begin{array}{l}
State-Action\quad Value\quad Q(S_t,A_t)=\mathbb{E}[\sum_{k=0}^\infty\gamma^kR_{t+k+1}|S_t=s,A_t=a]\\和状态价值函数类似，状态-动作价值函数也可以用累加收益的期望来刻画。\\只不过这个收益是在状态s采取具体动作a时产生的。
\end{array}
$$


## 3.算法

