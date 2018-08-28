---
title: "交叉熵损失函数"
collection: teaching
type: "人工神经网络教程-4"
permalink: /teaching/2018-NN-4
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-08-28
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1.单个神经元网络

为了简化，我们设计一个最简单的神经网络，单神经元网络。网络中只有一个神经元，如图4-1所示。该神经元的训练目的也非常简单。我们希望当输入数据1给这个神经元时，其输出为0。

![4-1](2018-NeuralNetwork/4-1.png)

*<center>图4-1 单神经元网络</center>*

根据[二次损失函数](http://duxinyu.cn//teaching/2018-NN-2)的定义，单神经元网络的损失函数为：
$$
C=\frac{(y-a)^2}{2}\tag{4-1}
$$
式中a为当输入x=1时神经元的输出值，y=0是输入值为1时神经元的期望输出值。这里激活函数我们选用sigmoid函数，因此，a=\sigma (z)，z=wx+b。将其带入式4-1并对损失函数求偏微分可得：
$$
\frac{\partial C}{\partial w}=(a-y)\sigma'(z)x=a\sigma'(z)\tag{4-2}
$$

$$
\frac{\partial C}{\partial b}=(a-y)\sigma'(z)=a\sigma'(z)\tag{4-3}
$$

sigmoid函数如图4-2所示，当其函数值接近1时，曲线非常平坦，因此函数的导数$$\sigma'$$非常小。所以导致式4-2和4-3的值非常小。这是导致学习速度降低的根本原因。

![1-3](2018-NeuralNetwork/1-3.png)

*<center>图4-2 sigmoid函数</center>*

# 2.交叉熵损失函数



![4-3](2018-NeuralNetwork/4-3.png)

*<center>图4-3 三个输入值的单神经元网络</center>*

为了避免学习速度降低，我们构建了一种新型的损失函数来代替二次损失函数。首先，将我们的单神经元网络稍微扩展一下，将输入量个数增加到三个，如图4-3所示。新的损失函数称为**交叉熵损失函数**的定义如下：
$$
C=-\frac{1}{n}\sum_x[ylna+(1-y)ln(1-a)]\tag{4-4}
$$
观察式4-4可以发现：1.函数值一定大于0。因为a和1-a的值都在[0,1]之间，因此lna和ln(1-a)小于0，所以C大于0。2. 对训练数据集中的所有数据x，其输出值非常接近期望输出值时，该损失函数值接近0。这两个性质和之前介绍的二次损失函数的性质相同，但是交叉熵损失函数具有更为优秀的性质，即学习速度不会变慢。我们通过对交叉熵损失函数求偏导数来证明这点：
$$
\frac{\partial C}{\partial w_j}=-\frac{1}{n}\sum_x(\frac{y}{\sigma(z)}-\frac{(1-y)}{1-\sigma(z)})\frac{\partial \sigma}{\partial w_j}=-\frac{1}{n}\sum_x(\frac{y}{\sigma(z)}-\frac{(1-y)}{1-\sigma(z)})\sigma'(z)x_j\tag{4-5}
$$
上式中$$\sigma'(z)=\sigma(z)(1-\sigma(z))$$，证明如下：


$$
\sigma(z)=\frac{1}{1-e^{-z}}\\ \sigma'(z)=\frac{\partial \sigma}{\partial z}=\frac{\partial \sigma}{\partial (1-e^{-z})}\frac{\partial (1-e^{-z})\sigma}{\partial e^{-z}}\frac{\partial e^{-z}}{\partial z}\\ =-\frac{1}{(1-e^{-z})^2}(-1)(-e^{-z})\\ =\frac{1}{1-e^{-z}}\frac{-e^{-z}}{1-e^{-z}}\\ =\sigma(z)(1-\sigma(z))
$$


将证明结果带入式4-5可得：
$$
\frac{\partial C}{\partial w_j}=\frac{1}{n}\sum_x x_j(\sigma(z)-y)\tag{4-6}
$$
与式4-2相比发现改用交叉熵损失函数后导致学习速度变慢的$$\sigma'(z)$$项消失了。并且惊喜的是不但学习速率不会变慢，而且具有输出和期望输出值差距$$\sigma(z)-y$$越大学习速率越快。