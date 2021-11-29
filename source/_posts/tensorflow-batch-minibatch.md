---
title: 如何理解TensorFlow中的batch和minibatch
date: 2016-09-20 01:47:30
tags:
- DeepLearning
- TensorFlow
- Tutorial
categories:
- tech
---

在刚开始学习使用TF的过程中，我不是很理解什么是`batch`。也经常有人问，到底`minibatch`是干什么的？
然而这是一个在TF中，或者说很多DL的框架中很常见的词。 [这个](http://keras-cn.readthedocs.io/en/latest/getting_started/concepts/)解释我觉得比较贴切也比较容易理解。引用如下：

> 深度学习的优化算法，说白了就是梯度下降。每次的参数更新有两种方式：
> 第一种，遍历全部数据集算一次损失函数，然后算函数对各个参数的梯度，更新梯度。
> 这种方法每更新一次参数都要把数据集里的所有样本都看一遍，计算量开销大，计算速度慢，不支持在线学习，这称为Batch gradient descent，批梯度下降。
> 另一种，每看一个数据就算一下损失函数，然后求梯度更新参数，这个称为随机梯度下降，stochastic gradient descent。
> 这个方法速度比较快，但是收敛性能不太好，可能在最优点附近晃来晃去，hit不到最优点。两次参数的更新也有可能互相抵消掉，造成目标函数震荡的比较剧烈。
> 为了克服两种方法的缺点，现在一般采用的是一种折中手段，mini-batch gradient decent，小批的梯度下降，这种方法把数据分为若干个批，按批来更新参数，
> 这样，一个批中的一组数据共同决定了本次梯度的方向，下降起来就不容易跑偏，减少了随机性。另一方面因为批的样本数与整个数据集相比小了很多，计算量也不是很大。

Michael Nielsen在[这一章节](http://neuralnetworksanddeeplearning.com/chap1.html#learning_with_gradient_descent)也有解释，mini-batch是什么，为什么有这个东西。
Deep Learning的[这一章节](http://www.deeplearningbook.org/contents/ml.html)的5.9小节也有解释，还给出了batch的典型值。
结合上面给出的中文解释，再看这两个小节，应该会对batch有所理解。
