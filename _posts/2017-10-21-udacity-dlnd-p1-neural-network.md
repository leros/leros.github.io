---
layout: post
title:  "DLND项目一：构建神经网络模型预测共享单车需求量"
date:   2017-10-21 21:43:55 -0500
---
本文是“Udacity深度学习课程项目常见问题系列”的第一篇，主要针对项目一：**搭建一个基本的神经网络，对共享单车的需求量进行预测**。这个项目的目的是让学员对神经网络的基本概念（包括梯度下降和反向传播）以及模型训练的基本环节有一定的认知。

---

**单元测试**

-   测试无法覆盖所有的情况，通过单元测试并不能确保项目执行没有问题

**Forward Pass**

-   这个项目是一个回归问题，最后输出的结果不需要bound到一个特定的范围里，因此不需要额外的激活函数，即`final_outputs = final_inputs`
-   如果是分类问题的话，需要通过激活函数把output转化为一定范围内离散的值。

**Backward Pass**

-   反向传播是神经网络的核心部分，也是整个项目的难点所在，建议将课程相关内容反复看几遍（Lesson 7 - Section15-16）
-   如果对一些词汇所指不太有把握，比如error和error term，建议把 Section 15的Working through an example部分好好看看
-   如果对具体的算法或者如何从数学公式转换为代码，建议仔细阅读Secton 16 - Implementing backpropagation，项目代码跟公式一一对应。
    ![math neural network](/assets/img/nn_math.png)

-   有必要的话，把矩阵的shape打印出来，帮助理解运算过程

**参数选择**

-   隐藏单元数目：一般不超过输入单元的2倍，但是也不能过小 - 过高可能引起overfitting， 而且开销很高，过小的话模型拟合可能会比较糟糕，[这里](https://www.quora.com/How-do-I-decide-the-number-of-nodes-in-a-hidden-layer-of-a-neural-network)有一篇关于隐藏单元数目的文章值得一读
-   学习速率`self.lr`的经验公式是：`self.lr / n_records` 趋近于 0.01, 这个项目`n_records`是128。
-   迭代次数：可以参考“training and validation loss”图，尽量让两条曲线下降并保持在水平状态，这个时候就可以停止training了。迭代次数太高有过拟合的风险，这个时候validation loss的曲线可能就不是水平的了，可能会向上升。

**学员反馈：**
Q：理解反向传播的数学公式还是有点困难，怎么办才好？觉得课程中没怎么推导公式，特别对于隐藏层的数学推导以及理解其误差反向传播过程有困难。
A: 反向传播的确不太直观，但背后的数学并不复杂，涉及到的概念包括偏导、链式法则等等，我当时在可汗学院上找了一些教程（比如partial derivatives, chain rule）复习了一下，然后尝试自己推导和理解课程中的数学公式。
