---
title: "过拟合的解决办法"
collection: teaching
type: "人工神经网络教程-4"
permalink: /teaching/2018-NN-4
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-08-28
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1.过拟合

在训练数据相对有限的情况下，训练好的神经网络可能会出现过拟合的问题。可以将其理解为由于训练数据有限，网络不能从中提炼出知识和规律，只是将训练数据死记硬背了下来。所以当对训练数据之外的数据进行运算时，其结果与实际情况会出现较大的偏差。就好比一个小朋友学习算数，我们只教给他1+1=2,1+2=3，这时他还没有真正明白加法的意义和规则。那么这时让他去算1+3=？的时候，结果往往是不正确的。

![4-1](2018-NeuralNetwork/4-1.png)

![4-2](2018-NeuralNetwork/4-2.png)

