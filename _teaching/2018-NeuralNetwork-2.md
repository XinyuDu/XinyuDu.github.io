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

当我们构建了一个神经网络后，其最初的权重$$W_{jk}^l$$和偏移量$$b_j^l$$都是随机的。这种网络并不能正确执行任务。我们还需要对其进行训练，使其在给定输入数据$$x$$时的输出$$o$$与$$x$$的标注值$$L(x)$$尽量相近。例如，我们输入数据$$x$$为一张猫的图片,则$$L(x)=cat$$，我们期望输出$$o=cat$$而不是$$o=dog$$。由于神经网络的输出值是向量，因此我们希望$$L(x)和o$$这两个向量之差的几何距离$$\parallel L(x)-o\parallel$$越短越好。