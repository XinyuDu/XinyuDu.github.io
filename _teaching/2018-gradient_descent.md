---
title: "梯度下降法"
collection: teaching
type: "神经网络教程-1"
permalink: /teaching/2018-gradient-descent
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-07-12
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1. 介绍

**梯度下降法**(gradient descent)是用来求函数最小值的方法，更准确的说是求**使得函数值最小的自变量大小**。例如函数f(x)求最小值，重要的不是函数的最小值具体是多少，重要的是x取什么值能使得函数值最小。这在许多工程类的优化问题中很有用。我们可以做一个类比的想象，函数值就是你的体脂率f(x<sub>i</sub>)，自变量就是你的训练方法x<sub>i</sub>，包括运动强度x<sub>1</sub>，运动时长x<sub>2</sub>....等。真正重要的事情是找到一种训练方法x<sub>i</sub>，使得你的体脂率f(x)最低。当然，自变量数目很少的情况下我们可以用函数求导的方法找到函数的极值点。但是当自变量个数很多的时候（比如自变量个数为百万或者千万，这在人工神经网络中很常见），用解析的方法计算函数极值开销极大。因此需要应用更加高效的梯度下降法来计算。

# 2. 算法

由于我实在无法想象一个三维以上的空间，因此这里我们用两个自变量的函数$$f(x_1,x_2)$$来介绍算法。