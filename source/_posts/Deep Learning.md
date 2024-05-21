---
layout: deep
title: Deep Learning
date: 2024-05-16 15:43:22
tags: 科研
---

## 1.什么是神经网络

* 神经元中装着的数字代表对应的像素的灰度值，0表示纯黑像素，1表示纯白像素。神经元中装着的数称为“激活值”。 
* 神经网络处理信息的**核心机制**是一层的激活值是**通过怎样的运算**算出下一层的激活值
* 所谓神经元的激活值实际上就是对**加权和到底有多正**的打分

![image-20240516164546935](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516164546935.png)

![image-20240516170232107](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516170232107.png)

  ![image-20240516171707220](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516171707220.png)

![image-20240516185215679](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516185215679.png)
$$
a^{(1)} = \sigma(Wa^{(0)} + b)
$$

* **权重**像是连接的强度，而**偏差**是该神经元是否倾向于活跃或不活跃。

## 2.梯度下降，神经网络如何学习|深度学习

需要考虑可以使用的所有数以万计的训练示例的平均成本，这个成本是衡量网络有多糟糕的标准

![image-20240516193200669](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516193200669.png)

![image-20240516193357520](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516193357520.png)

* 对于多变量微积分而言，**函数的梯度提供了最陡上升的方向**，应该朝着哪个方向迈进以最快的增加函数。相反，取负值可以提供最快的速度减小函数的方向。更重要的是该梯度向量的长度指示了最陡坡度的陡度。

* **最小化函数**的算法就是计算这个梯度反向，然后向下走一小步，然后不断重复这个过程

![image-20240516202129474](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516202129474.png)

* 有效计算梯度的算法被称为**反向传播**，它实际上是神经网络的核心
* **损失函数**是指用来衡量**模型的预测值和真实值之间的不一致程度的函数**

![image-20240516211400639](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516211400639.png)

**梯度**就是函数对它的各个自变量求偏导后，由偏导数组成的一个向量。

负梯度的每个分量告诉我们两件事，符号告诉我们输入向量的相应分量是否应该向上或者向下位移、**他编码了每个权重和偏差的相对重要性，也就是这些变化中哪一个将为您带来巨大收益**、如何改变所有连线上的权重偏置，使得损失下降的最快。

* 那么这个梯度向量求出来有什么意义呢？他的意义从几何意义上讲，就是函数变化增加最快的地方。具体来说，对于函数f(x,y),在点$$ (x_0,y_0)$$，沿着梯度向量的方向就是$(∂f/∂x_0 ,∂f/∂y_0 )^T $,是f(x,y)增加最快的地方。或者说，沿着梯度向量的方向，更加容易找到函数的最大值。反过来说，沿着梯度向量相反的方向，也就是$-(∂f/∂x_0 ,∂f/∂y_0 )^T $  的方向，梯度减少最快，也就是更加容易找到函数的最小值。
*  所以所谓的“梯度”就是求偏导，“梯度下降”就是沿着“导数值”减小的方向（负梯度方向），这样就可使得损失函数快速减小，误差就减小。在机器学习算法中，在最小化损失函数时，可以通过梯度下降法来一步步的迭代求解，得到最小化的损失函数和**模型参数值**.
* 所谓的学习就是指找到特定的权重偏置，从而使损失函数最小化

[小白零基础学习：详解梯度下降算法：完整原理+公式推导+视频讲解_标准梯度下降算法 损函数-CSDN博客](https://blog.csdn.net/zhouaho2010/article/details/102756411)

![image-20240516213607795](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516213607795.png)

如果想了解更多，我强烈建议您阅读这本由Michael Nielse写的书：[Neural networks and deep learning](http://neuralnetworksanddeeplearning.com/)

这本书讲解了视频中一些实例的代码，你可以在这里找到它：[mnielsen/neural-networks-and-deep-learning: Code samples for my book "Neural Networks and Deep Learning" (github.com)](https://github.com/mnielsen/neural-networks-and-deep-learning)

![image-20240516221816669](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516221816669.png)

## 深度学习之反向传播算法

* 梯度向量的每一项大小是在告诉大家代价函数对于每个参数有多敏感

![image-20240516222507331](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240516222507331.png)

* 代价函数牵扯到对成千上万个训练样本的代价取平均值，
