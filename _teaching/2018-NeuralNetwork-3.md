---
title: "反向传播算法"
collection: teaching
type: "人工神经网络教程-3"
permalink: /teaching/2018-NN-3
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-08-16
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1.训练速度

上一节在介绍如何[训练人工神经网络](http://duxinyu.cn//teaching/2018-NN-2)时介绍了一种加快训练速度的技术，<b>随机梯度下降法</b>。简单讲就是不对所有训练数据都进行损失函数对权重和偏移量的偏导数计算。而是从训练数据中选取一小部分进行偏导数计算然后求和再平均。用一部分训练数据的平局值代替全部数据的平均值，从而减小计算量加快训练速度，见式（2-5）和（2-6）。这在训练数据众多时可以起到很大作用。然而除了训练数据的大小外，训练一个神经网络的计算量还取决于网络的规模，即网络中权重和偏移量的个数。
$$
w_k'=w_k-\frac{\eta}{m}\sum_1^m\frac{\partial C_x}{\partial w_k}\tag{2-5}
$$

$$
b_l'=b_l-\frac{\eta}{m}\sum_1^m\frac{\partial C_x}{\partial b_l}\tag{2-6}
$$

利用随机梯度下降法我们可以将上述式子中对x求和的个数从n降到m，但是权重个数$$k$$和偏移量个数$$l$$并没有减小。因此当网络规模很大时训练速度依然很慢。



# 2.反向传播法(Backpropagtion)

如前所述，随机梯度下降法耗费计算量的大户是计算损失函数对权重和偏移量的偏导数$$\frac{\partial C}{\partial w_{jk}^l}, \frac{\partial C}{\partial b_j^l}$$。