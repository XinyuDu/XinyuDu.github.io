---
title: "神经元及神经网络的结构要素"
collection: teaching
type: "神经网络教程-1"
permalink: /teaching/2018-NN-1
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-07-13
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1. 神经元

神经元是神经网络的组成元素，任何结构的神经网络模型都是由若干个神经元以某种形式组成的。典型的神经元如图1-1所示。每个神经元可以有若干个输入和输出，一个神经元的输出可以连接到另一个神经元，变成另一个神经元的输入。因此输入和输出个数取决于有多少个神经元与该神经元相连接。为了简明图1-1只画了一个输出，图中黑色圆圈代表神经元，三条蓝色带箭头的线代表输入神经，红色带箭头的线代表输出神经。$$i_1,i_2,i_3$$为输入值，$$w_1,w_2,w_3$$为相对应的权重，$$b$$为偏置。

![1-1](2018-NeuralNetwork/1-1.png)

*<center>图1-1 神经元基本结构</center>*