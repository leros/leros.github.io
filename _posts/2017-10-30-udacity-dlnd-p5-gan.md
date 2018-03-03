---
layout: post
title:  "DLND项目五：构建GAN模型生成人像"
date:   2017-10-30 21:43:55 -0500
---

本文是“[Udacity深度学习课程项目常见问题系列](/udacity-dlnd-faqs/)”的第五篇，主要针对最后一个项目：利用对抗生成网络（Generative adversarial network, GAN)生成人脸图像。

---

**项目背景**

这个项目建立在第二个项目CNN的基础上，但巧妙的使用了两个CNN模型：generator(G)以及discriminator(D), 二者形成对抗。在对抗中，G要确保自己生成的图片，不被D识别出来是伪造的，而D之所以具备辨别的能力，是因为它从训练集中学习了真的图片应该是怎样的。更加有趣的是，G之所以具备生成图片的能力（或者说学习能力），是因为它根据D的反馈不断调整自己的参数，最终达到“愚弄”D的效果。整个模型架构非常有意思，也很有效，难怪Yann LeCun[认为](https://www.quora.com/What-are-some-recent-and-potentially-upcoming-breakthroughs-in-deep-learning)这个模型是深度学习近年来最重要的发展。 课程[gan_mnisty](https://github.com/udacity/deep-learning/tree/master/gan_mnist)，[batch-norm](https://github.com/udacity/deep-learning/blob/master/batch-norm/Batch_Normalization_Solutions.ipynb)， 以及 [dcgan-svhn](https://github.com/udacity/deep-learning/blob/master/dcgan-svhn/DCGAN.ipynb)部分为这个项目提供了很多示例，可以在项目中借鉴。

**常见问题**

`model_inputs(image_width, image_height, image_channels, z_dim)`

-   留意rank和data type，尤其是learning_rate应该是tf.float32类型的

`discriminator(images, reuse=False)`

-   和之前的CNN项目类似，但是增加了优化的技术，包括batch normalization以及leaky relu
-   根据Ian Goodfellow的[GAN讲义(P27)](https://arxiv.org/pdf/1701.00160.pdf)，第一层不需要使用batch normalization，这样模型才能了解到数据的均值和分布情况

`generator(z, out_channel_dim, is_train=True)`

-   generation是一个类似于将CNN倒过来的过程，最后输出图像
-   generation比discrimination要困难，所以网络也会更深一些才能取得不错的效果
-   最后一层不需要使用batch normalization

`model_loss(input_real, input_z, out_channel_dim)`

-   这个部分很有意思，也很容易出错，需要理解这几个loss的涵义
    -   d\_loss\_real：图片是真的（lable是1），D辨别出来图片是真的
    -   d\_loss\_fake：图片是伪造的（lable是0），D辨别出来图片是假的
    -   g_loss：G说图片是真的（lable是1），但是D认为图片是假的，意味着G成功骗过了D
-   这一部分Lesson 1的第13小节做了详细讲解，可参考

`model_opt(d_loss, g_loss, learning_rate, beta1)`

-   -   由于使用了batch normalization技术，需要更新全局统计量，可参考[课程示例](https://github.com/udacity/deep-learning/blob/master/dcgan-svhn/DCGAN.ipynb)，以及[官方文档](https://www.tensorflow.org/api_docs/python/tf/layers/batch_normalization)，以下是官方文档给的一个例子

update\_ops = tf.get\_collection(tf.GraphKeys.UPDATE_OPS)
  with tf.control\_dependencies(update\_ops):
    train_op = optimizer.minimize(loss)

`train(epoch_count, batch_size, z_dim, learning_rate, beta1, get_batches, data_shape, data_image_mode)`

-   Generator最后使用的是Tanh 激活函数，输出值在-1到1之间，但batch\_images的值在-0.5到0.5之间，因此需要乘以2，转化为-1到1，即batch\_images *= 2。如果你的模型生成的图片发灰，很可能是这里没有转化。

**参数选择**

-   由于限定了epoch，建议使用较小的batch size帮助模型充分学习
    -   对于MNIST数据集，最好把batch_size设在64或者32
    -   对于CelebA数据集，由于彩色图像本身蕴含的信息量更大，建议把batch_size设在32或者16
-   batch\_size和学习率相互关联，更改batch\_size往往意味着也要对学习率做一些调整
