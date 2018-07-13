---
title: "梯度下降法"
collection: teaching
type: "人工神经网络教程-0"
permalink: /teaching/2018-gradient-descent
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-07-12
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1. 介绍

**梯度下降法**(gradient descent)是用来求函数最小值的方法，更准确的说是求**使得函数值最小的自变量大小**。例如函数f(x)求最小值，重要的不是函数的最小值具体是多少，重要的是x取什么值能使得函数值最小。这在许多工程类的优化问题中很有用。我们可以做一个类比的想象，函数值就是你的体脂率f(x<sub>i</sub>)，自变量就是你的训练方法x<sub>i</sub>，包括运动强度x<sub>1</sub>，运动时长x<sub>2</sub>....等。真正重要的事情是找到一种训练方法x<sub>i</sub>，使得你的体脂率f(x<sub>i</sub>)最低。当然，自变量数目很少的情况下我们可以用函数求导的方法找到函数的极值点。但是当自变量个数很多的时候（比如自变量个数为百万或者千万级别，这在人工神经网络中很常见），用解析的方法计算函数极值开销极大。因此需要应用更加高效的梯度下降法来计算。

# 2. 算法

### 2.1 二元函数的梯度下降法

由于我实在无法想象一个三维以上的空间，因此这里用两个自变量的函数$$f(x,y)$$来介绍算法。假设你身处一个山谷中，你的目的是到达谷底$$f_{min}$$，但是由于大雾你并不能看清谷底在哪里。这种情况通常我们怎么做？我们会环视一圈周围的环境然后沿着下坡且坡度最大的方向走一步。然后再环顾四周选择下坡且坡度最大的方向再走一步。如此往复，直到发现我们身处的位置往任何方向走都是上坡路。在此过程中，每一步可以将其分解为沿着x方向的一步$$\Delta x$$和沿着y方向的一步$$\Delta y$$。那么走了这一步后你的所处位置的海拔高度下降为：
$$
\Delta f\approx\frac{\partial f}{\partial x}\Delta x+ \frac{\partial f}{\partial y}\Delta y\tag{1}
$$
我们只需要控制每步$$\Delta x, \Delta y$$的大小，使得$$\Delta f$$的值为负即可。这里我们定义一个参数$$\eta$$，称为步长（在神经网络中称为学习速率learning rate），其值为正。令
$$
\Delta x=-\eta\frac{\partial f}{\partial x},\Delta y=-\eta\frac{\partial f}{\partial y}\tag{2}
$$
将公式(2)带入(1)中得：
$$
\Delta f\approx-\eta(\lVert\frac{\partial f}{\partial x}\lVert^2+ \lVert\frac{\partial f}{\partial y}\lVert^2)\tag{3}
$$
从公式(3)可以看出，当我们令$$\Delta x, \Delta y$$像公式(2)那样取值的话，就能保证$$\Delta f$$为负。因此我们只需要按照公式(2)更新x和y的值就能最终取得函数f的最小值。
$$
x'=x-\eta\frac{\partial f}{\partial x}, y'=y-\eta\frac{\partial f}{\partial y}\tag{4}
$$
需要注意的是步长值不能太大，否则不能取得最小值。如同你每步迈1米远，是无法到达一个范围只有1米的山谷谷底的。但是步长取的太小又会使计算时间变长。实践中人们常常使用变步长的技术，即在运算过程中变换步长大小，从而高效率的取得最小值。

### 2.2 梯度下降法的一般形式

上面介绍了二元函数下的算法，但是该算法不止对二元函数有效，对任意n个自变量的函数都有效。假如有函数$$f(x_1,x_2,...,x_n)$$，我们将其自变量写成向量形式$$x\equiv(x_1,x_2,...,x_n)^T$$，则$$\Delta x\equiv(\Delta x_1,\Delta x_2,...,\Delta x_n)^T$$。令
$$
\nabla f\equiv(\frac{\partial f}{\partial x_1},\frac{\partial f}{\partial x_1},...,\frac{\partial f}{\partial x_n})^T
$$
则根据公式(1)有
$$
\Delta f\approx\nabla f\cdot\Delta x\tag{5}
$$
令：
$$
\Delta x=-\eta\nabla f\tag{6}
$$
则在n维空间中取得函数最小值的方法就是让自变量向量按下式不断迭代更新：
$$
x'=x-\eta\nabla f\tag{7}
$$

# 3. 总结

梯度下降法的精髓就是公式(2),(6)。它保证了$$\Delta f$$为负，这样往复进行下去就可以得到函数的最小值。可以将其理解为沿着函数梯度的反方向行走，最终达到谷底。算法具体的应用方法是根据公式(4),(7)不断更新位置，然后计算在新位置的函数值。