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

如前所述，随机梯度下降法耗费计算量的大户是计算损失函数对权重和偏移量的偏导数$$\frac{\partial C}{\partial w_{jk}^l}, \frac{\partial C}{\partial b_j^l}$$。数值计算偏导数的常用的方法是用差分代替微分：
$$
\frac{\partial C}{\partial w_j} \approx \frac{C(w-\epsilon e_j)-C(w)}{\epsilon} \tag{3-1}
$$
式中，$$\epsilon$$为一个很小的正数, $$e_j$$是j方向的单位向量。在计算（3-1）时需要利用式（1-10）来计算损失函数。即根据输入数据，从前往后一层层的推算出最终输出层的输出值，这个过程称为前向传播。因此，计算一个权重的偏导数需要进行两次前向传播过程来求出$$C(w-\epsilon e_j)和C(w)$$。所以假如网络有n个权重的话，整个计算量是n+1次前向传播，这还仅仅是针对一个训练数据的计算量。当网络中的神经元个数非常多时整个计算过程相当漫长。



因此我们需要用另外一种方法来考量，当一个神经元产生了微小扰动时，损失函数会如何变化。我们引入一个误差量$$\delta_j^l$$，其代表第l层，第j个神经元的误差。其定义如下：
$$
\delta_j^l=\frac{\partial C}{\partial z_j^l} \tag{3-2}
$$
其中$$z_j^l$$为输入到第l层，第j个神经元激活函数中的参数。根据式（1-7）$$z_j^l=\Sigma_k w_j^lo_k^{l-1}+b_j^l$$。上式等号右边考量的是第l层，第j个神经元激活函数的参数变化，引起损失函数的变化的速度。也就是说当有$$\Delta z_j^l$$产生时，损失函数会变化$$\frac{\partial C}{\partial z_j^l} \Delta z_j^l$$。现在我们利用式（3-2）来考察输出层L的情况，L层输神经元误差：
$$
\delta_j^L=\frac{\partial C}{\partial z_j^L} \tag{3-3}
$$
由于$$z_j^L$$并不是损失函数的直接自变量，它是通过激活函数来最终影响损失函数的值。因此，我们将式（3-3）进行一下变形，将激活函数引入：
$$
\delta_j^L=\frac{\partial C}{\partial a_j^L}\frac{\partial a_j^L}{\partial z_j^L} \tag{3-4}
$$
当激活函数为sigmoid函数时，$$a_j^L=\sigma(z_j^L)$$则上式等号右边第二项可以写成$$\sigma'(z_j^L)$$，因此上式可写为:
$$
\delta_j^L=\frac{\partial C}{\partial a_j^L}\sigma'(z_j^L) \tag{3-5}
$$


其矩阵形式表达为：
$$
\delta^L=\nabla_a C \odot \sigma'(z^L) \tag{3-I}
$$
其中$$\nabla_aC$$是梯度向量，由$$\frac{\partial C}{\partial a_j^L}$$组成。对二次损失函数来说$$\nabla_aC=(a^L-y)$$。 所以，对于二次损失函数来说式（3-I）可写为：$$\delta^L=(a^L-y)\odot \sigma'(z^L) \tag{3-I}$$。$$\odot$$是Hadamard算符，表示长度相等的两个向量对应元素相乘，如：
$$
\begin{bmatrix}a_1\\a_2\end{bmatrix} \odot \begin{bmatrix}b_1\\b_2 \end{bmatrix} = \begin{bmatrix} a_1*b_1 \\ a_2*b_2 \end{bmatrix} \tag{3-6}
$$
