---
title: "反向传播算法"
collection: teaching
type: "人工神经网络教程-3"
permalink: /teaching/2018-NN-3
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-08-16
location: "中国, 北京"
---

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/latest.js?config=TeX-MML-AM_CHTML">
</script>

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
其中$$z_j^l$$为输入到第l层，第j个神经元激活函数中的参数。根据式（1-7）$$z_j^l=\Sigma_k w_{jk}^la_k^{l-1}+b_j^l$$。式（3-2）等号右边考量的是第l层，第j个神经元激活函数的参数变化，引起损失函数变化的速度。也就是说当有$$\Delta z_j^l$$产生时，损失函数会变化$$\frac{\partial C}{\partial z_j^l} \Delta z_j^l$$。现在我们利用式（3-2）来考察输出层L的情况，L层输神经元误差：
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
其中$$\nabla_aC$$是梯度向量，由$$\frac{\partial C}{\partial a_j^L}$$组成。对二次损失函数来说$$\nabla_aC=(a^L-y)$$。 所以，对于二次损失函数来说式（3-I）可写为：$$\delta^L=(a^L-y)\odot \sigma'(z^L) $$其中$$\odot$$是Hadamard算符，表示长度相等的两个向量对应元素相乘，如：
$$
\begin{bmatrix}a_1\\a_2\end{bmatrix} \odot \begin{bmatrix}b_1\\b_2 \end{bmatrix} = \begin{bmatrix} a_1*b_1 \\ a_2*b_2 \end{bmatrix} \tag{3-6}
$$

到目前位置我们已经可以计算输出层L的误差$$\delta^L$$了，反向传播法顾名思义一下步就是如何计算L-1，L-2，...层的误差了。根据式（3-2），l+1层第k个神经元的误差为：
$$
\delta_k^{l+1}=\frac{\partial C}{\partial z_k^{l+1}}\tag{3-7}
$$
我们要做的就是找到式（3-7）和式（3-2）的关系，因此：
$$
\delta_j^l=\frac{\partial C}{\partial z_j^l} \\=\sum_k \frac{\partial C}{\partial z_k^{l+1}}\frac{\partial z_k^{l+1}}{\partial z_j^l}\\=\sum_k \frac{\partial z_k^{l+1}}{\partial z_j^l}\delta_k^{l+1} \tag{3-8}
$$
至此，我们找到了（3-7）和（3-2）的关系，即$$\delta_j^l$$和$$\delta_k^{l+1}$$的关系。但式中$$z_k^{l+1}$$的具体表达形式还不清楚。将其写成权重、激活函数和偏移量的表达形式：
$$
z_k^{l+1}=\sum_jw_{kj}^{l+1}a_j^l+b_k^{l+1}=\sum_jw_{kj}^{l+1}\sigma(z_j^l)+b_k^{l+1} \tag{3-9}
$$
对式（3-9）进行偏微分计算得：
$$
\frac{\partial z_k^{l+1}}{\partial z_j^l}=w_{kj}^{l+1}\sigma'(z_j^l) \tag{3-10}
$$
将上式带入式（3-8）则：
$$
\delta_j^l=\sum_kw_{kj}^{l+1}\delta_k^{l+1}\sigma'(z_j^l) \tag{3-11}
$$
其矩阵表达方式为：
$$
\delta^l=((w^{l+1})^T\delta^{l+1})\odot\sigma'(z^l)\tag{3-II}
$$
到此为止，我们可以利用（3-I）和（3-II）计算出任何一个神经元的误差了。但我们要的并不是误差，而是损失函数对权重和偏移量的偏导数。损失函数对偏移量的偏微分为：
$$
\frac{\partial C}{\partial b_j^l}=\frac{\partial C}{\partial z_j^l}\frac{\partial z_j^l}{\partial b_j^l}\tag{3-12}
$$
将式（3-2）带入上式，并参考式（3-9）对$$z_j^l$$做偏微分计算得到：
$$
\frac{\partial C}{\partial b_j^l}=\delta_j^l\tag{3-III}
$$
同样的，损失函数对权重的偏微分为：
$$
\frac{\partial C}{\partial w_{jk}^l}=\frac{\partial C}{\partial z_j^l}\frac{\partial z_j^l}{\partial w_{jk}^l}\tag{3-13}
$$
将式（3-2）带入上式，并参考（3-9）计算$$\frac{\partial z_j^l}{\partial w_{jk}^l}$$得到：
$$
\frac{\partial C}{\partial w_{jk}^l}=\delta_j^la_k^{l-1}\tag{3-IV}
$$
我们得到的（3-I）～（3-IV）就是<b>反向传播算法的四个基本公式</b>：
$$
\delta^L=\nabla_a C \odot \sigma'(z^L) \tag{3-I}
$$

$$
\delta^l=((w^{l+1})^T\delta^{l+1})\odot\sigma'(z^l)\tag{3-II}
$$

$$
\frac{\partial C}{\partial b_j^l}=\delta_j^l\tag{3-III}
$$

$$
\frac{\partial C}{\partial w_{jk}^l}=\delta_j^la_k^{l-1}\tag{3-IV}
$$

虽然公式（3-I）和（3-II）中使用了标准的sigmoid激活函数表达，但其对任何形式的激活函数都适用。当$$z_j^l$$的值远离0时，sigmoid函数值趋近于0或者1，此时$$\sigma'(z_j^l)\approx 0$$，这样就使得$$\delta_j^l$$的值很小，最终导致$$\frac{\partial C}{\partial w_{jk}^l}$$的值很小，我们称之为学习速度缓慢。为了提高训练速度，应该选择激活函数使其导数为大的正整数并且不会趋近于0，这样就可以避免训练速度低的问题。这也正是目前Relu函数被广泛使用的原因。这四个基本公式告诉我们，只要把网络中所有神经元的误差$$\delta_j^l$$计算一遍，就可以利用公式（3-IV）获得所有损失函数对权重的偏微分。即遍历计算一遍网络即可，而差分的方法需要遍历计算n+1次网络，n是神经元个数，因此计算速度大大提高。