---
title: "卷积神经网络CNN"
collection: teaching
type: "人工神经网络教程-6"
permalink: /teaching/2018-NN-6
venue: "杜新宇,中科院北京纳米能源与系统研究所"
date: 2018-09-06
location: "中国, 北京"
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 1.卷及神经网络CNN

在图像识别、分类等应用场景中，输入数据是图形，并且图像信息是与空间位置相关的。如下图所示，深色像素所在位置不同，代表着不同的数字。

<div align="center"><img src = "./2018-NeuralNetwork/6-1.png"/></div>

*<center>图6-1 MNIST手写数字训练集</center>*

因此，Yann LeCun在1998年提出了**卷积神经网络的（Convolutional Neural Network）**。与之前介绍的神经网络不同，CNN网络每层并不是由一列神经元组成，而是由一个二维面组成。典型的CNN由输入层，卷基层，池化层，全连接层和输出层组成，如下图所示。

![6-2](2018-NeuralNetwork/6-2.png)

*<center>图6-2 典型CNN结构</center>*

输入层是由若干神经元组成二维平面，每个神经元对应着图片相同位置的像素值（RGB或者灰度值）。卷基层神经元的值由感知野大小（图6-2感知野为橘红色方框，尺寸是3x3），步距（橘红方框从左到右，从上到下依次挪动的间隔），权重，和偏移量决定。因此，卷基层蓝色神经元的值为：
$$
a_{11}^2=\sigma(z)\\
z=a_{11}^1*w_{11}^2+a_{12}^1*w_{12}^2+...+a_{33}^1*w_{33}^2+b_{11}^2 \tag{6-1}
$$
在卷基层中，每个神经元对应着3x3个权重和1个偏移量，在感知野移动过程中，这些权重和偏移量保持不变。这套不变的权重和偏移量称为卷积核或者滤波器。不同的卷积核对应不同的卷基层。因此，一个输入层可以导出n个卷基层，n为卷积核的数量，图6-3所示。图6-2中池化层神经元由池化操作获得，其值为卷基层中绿色方框中四个神经元的最大值，称为最大池化（max-pooling）。除了最大池化外还有L2池化，平均池化等。由图6-2可以看到，池化尺寸为2x2（绿色方框），步距为2，因此池化层的尺寸为5x5个神经元。

<div align="center"><img src = "./2018-NeuralNetwork/6-3.png"/></div>

*<center>图6-3 多卷积核生成的多个卷基层</center>*

# 2.深度神经网络

学者研究表明，增加神经网络的层数可以有效提高模型精度，并且可以让神经网络完成更为复杂的任务。著名的AlphaGO Zero能够以100-0的战绩战胜众多人类围棋大师。其算法就是基于深度神经网络，开发AlphaGo Zero的公司就叫DeepMind。AlphaGo Zero的网络深度达到了80多层，其中就包含了若干个感知野大小为3x3，卷积核为256个，步距为1的CNN层。<sup>1<sup>

毫无疑问像AlphaGo Zero这种非常复杂的网络可以称为深度神经网络。但到底多少层就可以称为深度网络呢？目前学术界并没有一个统一的共识。一般讲超过一个隐藏层的神经网络都可以称为深度神经网络。深度度越深，精度会越好，但训练模型的难度越大，因此也不能无限度的扩展模型的深度。这个项目[crack_natapp](https://github.com/XinyuDu/crack_natapp)，用一个3层卷积神经网络（如图6-4所示）就可以识别屏幕上的6位英文字符或数字，并且拥有很高的准确度>99%。

<div align="center"><img src = "./2018-NeuralNetwork/6-4.png"/></div>

*<center>图6-4 多层卷积神经网络结构</center>*

# 参考文献

1.Silver, D., Schrittwieser, J., Simonyan, K., Antonoglou, I., Huang, A., Guez, A., … Sifre, L. (2017). Mastering the game of Go without human knowledge. *Nature*, 550(7676), 354–359. https://doi.org/10.1038/nature24270