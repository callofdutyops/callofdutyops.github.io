---
title: TensorBoard Embedding 实战
date: 2017-03-11 23:16:15
tags:
- DeepLearning
- TensorFlow
- Tutorial
categories:
- tech
---

TensorBoard是TensorFlow自带的一个训练可视化工具，可以实现对训练过程中的tensor量监控，
常见的包括loss，accuracy等等，同时也可以对权重值W，偏置值b等绘制histogram（不过这个不是传统意义上的histogram，[开发组也在着手让这个histogram变得更直观](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tensorboard#distribution-dashboard)）。
r0.12版本中添加了新功能embedding，随着r1.0的释出，embedding也越发成熟，但是[官网的教程](https://www.tensorflow.org/get_started/embedding_viz)确实让新手一头雾水，
几番搜索后，发现了[一份不错的示例代码](https://gist.github.com/dandelionmane/4f02ab8f1451e276fea1f165a20336f1)，但是这其中还是有些比较隐晦的概念，
因此这篇博客针对对mnist的embedding进行讲解。

## 预备知识：
1. 什么是embedding？
首先，可以看看[wiki的定义](https://zh.wikipedia.org/wiki/%E5%B5%8C%E5%85%A5_(%E6%95%B0%E5%AD%A6))，
其实简而言之，embedding就是一个“保持结构的单射”，保持结构是指不改变原始数据的结构信息（比如几何结构，mnist图像的内在结构），单射是指一一映射，
这样就很好理解什么叫对mnist进行embedding，也就是对mnist原始图像进行一个“保持结构的单射变换”，使得一个图像这种非结构型数据易于处理（分类），
如果对词嵌入很熟悉的话，就很好理解了，词嵌入也是一种embedding，把词映射到了实数空间。
2. 什么是sprite image
首先看一下[w3schools的定义](https://www.w3schools.com/css/css_image_sprites.asp)，这个其实在网页和游戏里比较常用，就是把很多零碎的小图片拼接成一张大图片，
要用到相应图片的时候就去大图片上截取。例如，1024张mnist图像拼接的sprite image是这样的：
![](/images/sprite.png)

接下来我就先说一下TensorBoard Embedding的基本思想，基本代码操作，然后给出完整代码以及用到的资源。
（请先阅读上面给出的官方教程，有很多概念需要了解，例如sprite image以及labels文件的内容，组织方式等。）

TensorBoard Embedding的基本思想就是embedding + sprite image。
首先，输入（例如mnist图像以及对应的标签）经过模型（例如卷积神经网络）得到输出（一个对图像重表示的tensor），
这个输出的tensor其实就可以看做对一个mnist输入的embedding（1.由图像结构计算出的。2.每个图像有且仅有一个对应的输出tensor）。
那么，我们就可以利用这个embedding对原始的mnist图像进行分类，可视化处理。
随后，指定sprite image的路径以及对应的labels的路径，利用给出的labels去把输出的tensor绑定到对应的sprite image区块的图像。
这么说有点绕，举个例子：mnist的有监督训练，网络模型接收了一个mnist图像`7`以及对应这个图像的标签`7`，经过模型，得到输出tensor `t`，
那么TensorBoard就会去给定的labels文件里寻找`7`这个标签，然后找到`7`这个标签对应的sprite image中的区块（行优先），
绑定到tensor `t`上，这样，对tensor进行可视化的时候就可以显示绑定的image，达到对image进行分类可视化的效果。 

实现的关键代码是（解释详见注释）：
```python
import tensorflow as tf

config = tf.contrib.tensorboard.plugins.projector.ProjectorConfig()
embedding_config = config.embeddings.add()
# embedding是一个tf变量
embedding_config.tensor_name = embedding.name
# sprite image文件路径
embedding_config.sprite.image_path = sprite_path
# labels文件路径
embedding_config.metadata_path = labels_path
# sprite image中每一单个图像的大小
embedding_config.sprite.single_image_dim.extend([28, 28])
# 写入配置
tf.contrib.tensorboard.plugins.projector.visualize_embeddings(writer, config)
```

从代码中可以看出，关键在于，embedding变量，sprite image文件路径，labels文件路径。
具体的实现详见[示例代码](https://gist.github.com/dandelionmane/4f02ab8f1451e276fea1f165a20336f1)。
