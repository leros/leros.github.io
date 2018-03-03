---
layout: post
title:  利用Conda配置DLND课程项目运行环境
date:   2017-11-2 21:43:55 -0500
---

对DLND项目而言，最重要的两个package是Python和Tensorflow，由于Tensorflow发展很快，从年初的1.0现在已经升级到了1.4，而利用conda安装的Tensorflow也往往保持了这个节奏。但不少课程项目都是基于1.0或者1.1的，就出现了一些因为Tensorflow版本引起的问题。下面介绍一种简单的方式，可以根据自己的需要创建所需的环境，这里以创建一个Python3.6 + Tensorflow1.0.0的环境为例。假设使用的是AWS g2.2xlarge GPU instance，以及Udacity 的udacity-dl镜像(ami-3a603b5a)

-   第一步：利用conda新建一个名为tf1.0-py3.6的新环境，指定Python版本，这里选择了3.6

`conda create --name tf1.0-py3.6 python=3.6`

-   第二步：激活刚创建的这个环境

`source activate tf1.0-py3.6`

-   第三步：安装Tenforflow，假设安装的是支持GPU的Tensorflow 1.0.0版

`pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.0.0-cp36-cp36m-linux\_x86\_64.whl`

这一步参考了[Tensorflow官网的安装指南](https://www.tensorflow.org/install/install_linux#InstallingAnaconda)，[这里](https://www.tensorflow.org/install/install_linux#the_url_of_the_tensorflow_python_package)列举了不同Python版本以及有无GPU时安装Tensorflow所需的网址，比较关键的是**tensorflow_gpu-1.0.0**这一部分，假如如果要安装最新的（截至2017年11月）1.4.0版，可以使用这个网址：https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.4.0-cp36-cp36m-linux\_x86\_64.whl 安装好之后，可以新建一个notebook，利用课程项目里检测环境的代码检测一下安装的结果：

```Python
"""
DON'T MODIFY ANYTHING IN THIS CELL
"""
from distutils.version import LooseVersion
import warnings
import tensorflow as tf

\# Check TensorFlow Version
assert LooseVersion(tf.\_\_version\_\_) >= LooseVersion('1.0'), 'Please use TensorFlow version 1.0 or newer.  You are using {}'.format(tf.\_\_version\_\_)
print('TensorFlow Version: {}'.format(tf.\_\_version\_\_))

\# Check for a GPU
if not tf.test.gpu\_device\_name():
    warnings.warn('No GPU found. Please use a GPU to train your neural network.')
else:
    print('Default GPU Device: {}'.format(tf.test.gpu\_device\_name()))
```
Results:
```
TensorFlow Version: 1.0.0
Default GPU Device: /gpu:0
```
