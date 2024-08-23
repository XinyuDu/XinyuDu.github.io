---
title: "PPO算法"
collection: teaching
type: "强化学习教程-5"
permalink: /teaching/2021-RL-5
venue: "杜新宇,京东"
date: 2021-06-23
location: "中国, 北京"
---

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/latest.js?config=TeX-MML-AM_CHTML">
</script>

## 1.PPO-Proximal Policy Optimization

PPO算法的思想继承自TRPO(Trust Region Policy Optimization)， 基本思想是更新策略模型的参数时，保证新策略的分布与旧策略的分布相差不要太大。TRPO用了KL散度（KL-Divergence）度量新旧两个策略的分布相差的程度。代码实现起来比较复杂，PPO用了更简化的Clip方法保证了同样的效果，而且表现并不比TRPO差。

## 2.数学推导

PPO通过如下公式更新策略模型的参数：

$$
\theta_{k+1} = arg\max \limits_{\theta} \mathbb{E}[L(s,a,\theta_k,\theta)]
$$

即，利用梯度下降法更新参数$$\theta$$，使得目标函数$$L$$取得最大值。其中，目标函数形式如下：


$$
L(s,a,\theta_k,\theta) = min[\frac{\pi_\theta(a | s)}{\pi_{\theta_k}(a | s)}A^{\pi_{\theta_k}}(s,a), clip(\frac{\pi_\theta(a | s)}{\pi_{\theta_k}(a | s)},1-\epsilon,1+\epsilon)A^{\pi_{\theta_k}}(s,a)]
$$

其中$$A^{\pi_{\theta_k}}$$是优势函数，$$\frac{\pi_\theta(a \mid s)}{\pi_{\theta_k}(a \mid s)}$$是新旧策略比值，clip函数有三个参数，第一个参数是输入值，第二个参数是下限，第三个参数是上限。如果输入值处于上限和下限之间，clip输出值就等于输入值。如果小于下限，则输出下限值。如果大于上限，则输出上限值。$$\epsilon$$是超参数，其限定了新旧策略相差的程度，即新策略能离旧策略多远。

上式相对复杂，可以将其做如下简化：


$$
L(s,a,\theta_k,\theta) = min\{\frac{\pi_\theta(a | s)}{\pi_{\theta_k}(a | s)}A^{\pi_{\theta_k}}(s,a), g[\epsilon,A^{\pi_{\theta_k}}(s,a)]\} \\
\begin{equation}
g(\epsilon,A)=\left\{
\begin{aligned}
(1+\epsilon)A,A \ge 0 \\
(1-\epsilon)A,A < 0 \\
\end{aligned}
\right.
\end{equation}
$$


我们通过具体的例子来理解该目标函数。假设agent处在状态s时采取了动作a，形成了一个对儿(s, a)。让我们看看目标函数该如何计算取值。

**优势函数为正：**假如在该(s,a)下，优势函数A为正，则上式简化为：


$$
L(s,a,\theta_k,\theta) = min[\frac{\pi_\theta(a | s)}{\pi_{\theta_k}(a | s)},(1+\epsilon) ]A^{\pi_{\theta_k}}(s,a)
$$


因为优势函数是正的，所以目标函数会随着$$\pi_{\theta}(a \mid s)$$的增大而增大。然而，当新旧策略的比值大于上限值时，min函数发挥作用，目标函数获得最大上限值$$(1+\epsilon) A^{\pi_{\theta_k}}(s,a)$$。因此，新策略不再试图离旧策略更远以获得目标函数值的增长。

**优势函数为负：**假如在该(s,a)下，优势函数A为负，则上式简化为：


$$
L(s,a,\theta_k,\theta) = max[\frac{\pi_\theta(a | s)}{\pi_{\theta_k}(a | s)},(1-\epsilon) ]A^{\pi_{\theta_k}}(s,a)
$$
注意，优势函数为负，把它提到min函数外时，min函数变成max函数。因为，优势函数为负，所以目标函数会随着$$\pi_{\theta}(a \mid s)$$的减小而增大。然而，当新旧策略的比值小于下限值时，max函数发挥作用，目标函数获得最大上限值$$(1-\epsilon) A^{\pi_{\theta_k}}(s,a)$$。因此，新策略不再试图离旧策略更远以获得目标函数值的增长。

clip函数，或者g函数充当了一个正则的角色。通过取消激励来避免策略更新时变化巨大。超参数$$\epsilon$$规定了新策略能离旧策略多远。



## 3.算法逻辑

1. 初始化策略模型参数$$\theta_0$$，初始化价值模型参数$$\phi_0$$

2. for k=0,1,2,...do
   
   ​    用策略$$\pi_{\theta_k}$$与环境互动生成数据，$$\mathcal{D}_k={\tau_i}$$
   
   ​    计算收益$$\hat{R}_t$$
   
   ​    利用价值模型$$V_{\phi_k}$$计算优势函数的估计值$$\hat{A}_t$$
   
   ​    通过最大化PPO-Clip目标函数来更新策略：
   
   $$
   \theta_{k+1} = arg\max \limits_{\theta} \frac{1}{|\mathcal{D}_k|T}\sum_{\tau \in \mathcal{D}_k}\sum_{t=0}^Tmin\{\frac{\pi_\theta(a | s)}{\pi_{\theta_k}(a | s)}A^{\pi_{\theta_k}}(s,a), g[\epsilon,A^{\pi_{\theta_k}}(s,a)]\}
   $$
   
   ​    通过最小化均方差MSE，来更新价值模型的参数：
   
   $$
   \phi_{k+1} = arg \min\limits_{\phi}\frac{1}{|\mathcal{D}_k|T}\sum_{\tau \in \mathcal{D}_k}\sum_{t=0}^T[V_\phi(s_t)-\hat{R}_t]^2
   $$

3. end for

