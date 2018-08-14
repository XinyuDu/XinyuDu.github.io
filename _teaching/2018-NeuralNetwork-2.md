---
title: "通过最小化损失函数训练神经网络"
collection: teaching
type: "人工神经网络教程-2"
permalink: /teaching/2018-NN-2
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-08-14
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1. 损失函数

当我们构建了一个神经网络后，其最初的权重$$w_{jk}^l$$和偏移量$$b_j^l$$都是随机的。这种神经网络并不能正确执行任务。我们还需要对其进行训练，使其在给定输入数据$$x$$时的输出$$o$$与$$x$$的标注值$$L(x)$$尽量相近，这种用于训练神经网络的数据集称为训练数据集。例如，我们输入数据$$x$$为一张猫的图片,则$$L(x)=cat$$，我们期望输出$$o=cat$$而不是$$o=dog$$。由于神经网络的输出值是向量，因此我们希望$$L(x)和o$$这两个向量之差的几何距离$$\parallel L(x)-o\parallel$$越短越好，几何距离越短神经网络的实际输出和我们期望的输出越相近。而且我们希望神经网络在训练数据集的所有训练数据上都实现短的几何距离，即要求$$\sum_{i=1}^n \parallel L(x)-o\parallel$$尽量小。我们可以将权重和偏移量当做自变量构建一个函数，来用前面介绍的梯度下降法来获得最小值。