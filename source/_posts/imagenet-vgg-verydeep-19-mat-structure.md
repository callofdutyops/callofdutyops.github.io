---
title: imagenet-vgg-verydeep-19.mat 格式解析
date: 2017-08-18 21:11:08
tags:
- DeepLearning
- TensorFlow
- VGG
categories:
- tech
---

> [VGG pretrained模型地址](http://www.vlfeat.org/matconvnet/pretrained/)
> [下载地址](http://www.vlfeat.org/matconvnet/models/imagenet-vgg-verydeep-19.mat)
> 不过我用的这个[beta版本](http://www.vlfeat.org/matconvnet/models/beta16/imagenet-vgg-verydeep-19.mat)不太一样，但是分析过程是一样的

今天在玩Stanford CS20SI公开课里的一个神经网络style_transfer小模型的时候，用到了vgg pretrained模型，然而，代码里获取weight和bias
是这么[写的](https://github.com/chiphuyen/stanford-tensorflow-tutorials/blob/master/assignments/style_transfer/vgg_model.py#L17):
```python
W = vgg_layers[0][layer][0][0][2][0][0]
b = vgg_layers[0][layer][0][0][2][0][1]
```

摔！这也没注释，完全看不懂啊！
虽然可以查到vgg的[模型信息](http://www.robots.ox.ac.uk/~vgg/research/very_deep/)(下面有个layer configuration的SVG图)，可是对应不上这些索引啊！
于是把模型下载下来，在ipython里一步一步解析：
```python
import scipy.io
vgg = scipy.io.loadmat('imagenet-vgg-verydeep-19.mat')
```

接下来就是探索格式了。

首先，看一下导入后的模型类型: `type(vgg)`，嗯，不出所料，是个`dict`，`scipy.io.loadmat`的官方文档也有说明，返回类型是个dict。

然后就是看一下有哪些key啦：`vgg.keys()`，输出有`dict_keys(['__header__', '__version__', '__globals__', 'layers', 'classes', 'normalization'])`。

看来前三项是meta信息，后面的三样东西是我们需要的，看名字应该分别是层参数，分类信息，正则化参数(像素平均值)。

那么，先看看layers层: `layers = vgg['layers']`，输出有一大坨，不过看头部足以分析: 
```text
array([[array([[(array([[array([[[[0.39416704, -0.08419707, -0.03631314, ...
```

顶级array有两个`[[`所以是两维，每一个维数的元素是array，array内部还有维数，因此可以看出，想要索引出元素确实需要很多index，那么看一下shape：`layers.shape`，输出是 `(1, 43)`。

说明虽然有两维，但是第一维是"虚的"，也就是只有一个元素，我们index进去：`layers = layers[0]`。

根据模型可以知道，这43个元素其实就是对应模型的[43层信息](http://www.chioka.in/tensorflow-implementation-neural-algorithm-of-artistic-style)(conv1_1, relu, conv1_2...)，
那么看一层就足以，而且我们现在得到了一个有用的index，那就是layer：`layers[layer]`。

其中layer是layer参数，范围0-42，我们选0看看：`layer = layers[0]`，从输出可以看出尾部有`dtype`信息，这个比较重要，记录了元素的标签：
```text
dtype=[('weights', 'O'), ('pad', 'O'), ('type', 'O'), ('name', 'O'), ('stride', 'O')]
```

可以看出顶层的array有5个元素，分别是`weight`(含有bias)，`pad`(填充元素,无用)，type, name, stride信息。
然后继续看一下`shape`信息, `layer.shape`输出是：`(1, 1)`。说明虽然有两维但是这两维都是"虚的"，也就是只有一个元素，我们直接两次index进去，
应该会得到一个包含5中信息的array，`layer = layer[0][0]`，现在layer已经是"裸"的了，直接看看len，`len(layer)`输出是`5`果然不出所料，
那么已经很清楚了，对应上面的`dtype`说明，很容易得到weight, bias之类信息，比如layer的name，是第4个(从0开始索引)，那么：`name = layer[3]`
输出是`array(['conv1_1'], dtype='<U7')`，果然拿到了name信息，但是这是一个array，想拿到字符串还要再index进去：`name = name[0]`。
这次输出就是一个字符串元素了：'conv1_1'，看来解析已经成功了。
其他如法炮制，例如weight(含有bias)：`weight = layer[0]`，看一下weight的shape：`weight.shape`，输出是: `(1, 2)`，
再次说明第一维是"虚的"，index进去：`weight = weight[0]`。注意，这里的weight是广义的weight，也就是包含通常我们说的weight和bias，
所以，分别拿到这两个元素还要再解开一次：
```python
weight, bias = weight
```

至此，已经完成了第0层的name, weight, bias解析，那么其他层也一样，改变layer的index即可，但是要注意的是，这是conv层的解析，relu层，fc层有所不同，不过原理一样，在ipython里探索即可。

所以，根据上面总结出layer的name的index顺序：
```python
name = vgg
# 字典key
['layers']
# 去掉"虚"的维，相当于np.squeeze
[0]
# 层索引
[layer]
# 连续两次去掉虚的维度
[0][0]
# weigh, pad, type, name, stride5类信息的索引，从0开始
[3]
# 去掉"虚"的维,相当于np.squeeze
[0]
```

weight，bias的index顺序：
```python
weight, bias = vgg
# 字典key
['layers']
# 去掉"虚"的维，相当于np.squeeze
[0]
# 层索引
[layer]
# 连续两次去掉虚的维度
[0][0]
# weigh, pad, type, name, stride5类信息的索引，从0开始
[0]
# 去掉"虚"的维，相当于np.squeeze
[0]
```
