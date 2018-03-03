---
layout: post
title:  "DLND项目三：利用RNN模型生成电视剧本"
date:   2017-10-25 21:43:55 -0500
---
本文是“Udacity深度学习课程项目常见问题系列”的第三篇，主要针对项目三：构建一个卷积神经网络（Recurrent Neural Network， RNN）来生成电视剧本。这个项目跟课堂讲过的[Anna KaRNNa](https://github.com/udacity/deep-learning/blob/master/intro-to-rnns/Anna_KaRNNa.ipynb)例子很接近，区别在于Anna KaRNNa是以字符为单位来推演，而这个项目是以词为单位来推演。 以下为该项目易犯的错误及常见的问题。

---

`create_lookup_tables(text)`

-   用以建立一个单词和编号一一对应的关系，词频并不重要，所以不需要额外计数和排序，先用\`set(text) \`去重即可

`get_inputs()`

-   需要留意inputs、targets、及learning_rate的类型和rank

`get_init_cell(batch_size, rnn_size)`

-   不推荐使用dropout，因为dropout主要是为了防止overfitting，但这个项目很可能出现underfitting，即模型对数据的拟合不够好
-   initial_state的类型应该是tf.float32
-   项目数据量较小，叠加BasicLSTMCell，至多两层就够了

`build_nn(cell, rnn_size, input_data, vocab_size, embed_dim)`

-   rnn_size是一个多余的参数
-   get\_embed 函数的最后一个参数应该是 embed\_dim

`get_batches(int_text, batch_size, seq_length)`

假定input是`[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15], 2, 3`, 最后的输出是：
```
    [
      # First Batch
      [
        # Batch of Input
        [[ 1  2  3], [ 7  8  9]],
        # Batch of targets
        [[ 2  3  4], [ 8  9 10]]
      ],

      # Second Batch
      [
        # Batch of Input
        [[ 4  5  6], [10 11 12]],
        # Batch of targets
        [[ 5  6  7], [11 12 13]]
      ]
    ]
  ```

-   几点观察
    -   上面这个例子里batch\_size或者说一个batch里的input有两组(这个例子最后刚好得到了2个batch，不要跟batch\_size混淆）
    -   每一段input的长度是3，每一个batch又有input batch和target batch，比如\[1, 2, 3\]对应的是\[7, 8, 9\]
    -   实际用到的元素数量是(len(int\_text) // (batch\_size * seq\_length) ) \*(batch\_size * seq\_length) + 1, 最后加一是因为target要往后顺延1，因此int\_text的最后两个元素14及15被丢弃了

**参数选择**

-   epochs: 和之前的项目类似，根据loss或者accuracy的变化趋势做判断，如果不再变化或者变化幅度很小，就可以停止训练了
-   batch_size: 具有适当的 batch size， 它要足够大使得神经网络能够高效地训练，同时也不能过大否则内存会不够。此处并没有所谓“最好的”值，它往往受你 GPU 显存的影响， 这里128或者256都可以
-   embed_dim一般跟词汇量匹配
-   rnn_size : RNN 层的大小（即隐层中节点的数量）足够大，使之能够很好的拟合数据。同样地，此处也没有标准的最优值，同样128或者256都不错
-   seq_length: 序列长度应大约是你希望生成句子的长度，这个项目设为12-15都可以
-   learning_rate: 学习率不能太大，如果太大的话那么训练算法将不会收敛。不过也要是适当的值，确保算法不会无限制地训练，0.001是个不错的开始，可以根据收敛速度做一些调整。
