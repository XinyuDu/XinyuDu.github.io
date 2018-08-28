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
式中$$a$$为当输入值为1时神经元的输出值，y=0是输入值为1时神经元的期望输出值。这里激活函数我们选用sigmoid函数，因此$$a=\sigama (z)$$，$$z=wx+b$$。