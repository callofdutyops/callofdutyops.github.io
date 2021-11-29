---
title: 结合TensorFlow PlayGround的简单神经网络原理解释
date: 2016-10-15 14:47:02
tags:
- DeepLearning
- TensorFlow
- Tutorial
categories:
- tech
---

很多人把机器学习（或者说本篇文章的重点：神经网络）看成“黑技术”、“黑魔法”，其实不然，所有的机器学习无外乎就是找到从输入到输入的**最佳拟合函数**，
只是各种不同的机器学习技术得到的拟合函数方法、效果不同因此适用情景不同罢了。
那么今天我们就结合TensorFlow的Playground来具体讲解一下，神经网络是如何去拟合输入和输出之间的目标函数的。好的，系好安全带，要发车了：）

[PlayGround](https://playground.tensorflow.org/)是一个图形化用于教学目的的简单神经网络在线演示、实验的平台，非常强大地可视化了神经网络的训练过程。
不得不说做出这个演示系统的人不仅对神经网络的见解深刻，更是找到了可视化的有力方法。建议使用的时候把右下角的discretize output（离散化输出）选上，不去让系统自动平滑，这样本质看的更清楚。

我们首先简单的讲一下这个PlayGround。
第一块也就是最上面一行是训练参数，保持默认即可，这个我们不去做过多解释和介绍。
因为这涉及到更深层次的神经网络知识，远非一篇简短的blog可以解释清楚，而且本篇文章的重点也是从intuitive（直觉化，直观化）方式讲解基本原理。
想要了解更多的内容，请参见我写的另一篇教程：[新手向的TensorFlow学习之路（Learning paths）](http://143.198.131.119/index.php/2016/07/06/tensorflow-learning-paths/)。
第二块就是下面的内容，从左到右是dataset（数据集）选择，feature（特征）选择，hidden layers（隐含层）选择和output（输出）。

研究一个未知事物的时候，我们习惯于也应该从最简单的情况开始讨论，那么对于神经网络，什么是简单的情况？
当然是神经元的数目越少越好，这样我们才能从繁杂的现象中理清关系。首先我们选择dataset中的第二行第一个，也就是Gaussian，
然后feature选择x1，x2，点击hedden layers的减号去掉所有的隐含层，这样我们就得到的简单的模型，这个模型简单到只有两个神经元！
就像这样：
![playground1](/images/playground1.png)
接下来，我们点击第一行的Run开始训练。这个模型应该几秒钟就训练完成了。而且效果非常好，可以清晰的看出dataset已经被明显地按照颜色划分成了两块。
你可能会觉得，What！！发生了什么！！别急，我们接着看。

这时把鼠标hover（悬停）在x1或者x2上，你会看到这样的划分：
![playground2](/images/playground2.png)
对，这就是第一个feature，竖着“切一刀”！同样，第二个也是“切一刀”，不过是横着罢了。这样简单的“切一刀”就能解决问题吗？显然不能，所以这时时候weight（权重）就是上场了。
weight就是来协调每个“切一刀”切多少的，把每个简单的动作联合起来，共同去解决问题。也就是说：

**每个神经元都是“傻傻的”只会一件事情，然后通过weight去组合协调，完成一件复杂的任务！**

多说无益。我们更进一步看看吧。 我们再把鼠标hover到那个连接x1，x2到output的线条上，这时你会看到“Weight is xxx”的字样，点击线条我们是可以修改那个xxx数值的。
那我们就试着修改那个xxx吧！首先，把x1的weight改成0，你应该会看到这样的画面：
![playground3](/images/playground3.png)

这不就是x2嘛！是不是有点感觉了？如果x2的weight是0那么应该和x1一样吧？那就来试试自己的猜想，点击第一行的reset（重置）后run重新训练，再试着把x2的weight改成0。
这次发现，不出所料，果真是自己猜想的那样～这次我们把x1，x2的weight都改成1，当然如果你开心，可以都改成100：）你会发现，一条完美的对角线平分了dataset，把dataset分成了两个部分。
就像这样：
![playground4](/images/playground4.png)

这意味着什么？这意味着两点：
1. weight确实是各个feature的加权值，是一个去协调各个feature的量，weight(x1)=weight(x2)的时候，x1，x2同等重要，导致平分天下
2. 学习/训练的过程不过是去寻找选择最优weight的过程，如果“人眼”能一眼看出weight，就像这个简单的数据集，我们大可去手工输入weight，同样可以达到一样甚至更好的效果！

这正验证了之前的结论：

**每个神经元都是“傻傻的”只会一件事情，然后通过weight去组合协调，完成一件复杂的任务！**

两个神经元尚且如此，想象一个拥有成千上万乃至上亿神经元的系统（比如人脑），完全不用奇怪这个系统可以完成及其复杂的任务！
现在可以尝试不同的dataset，再多加几个hidden layers，感受一下简单的神经元通过weight组合协调完成分类任务的强大。
推荐试一下这个组合：dataset选择第一行第二个Exclusive or，feature只选择一个，就是第五个x1x2，然后hidden layers为0，也就是没有隐含层。
然后再试一下用其他的feature去训练，对比感受一下feature的选择对结果的巨大影响。

讲到这里，应该对神经网络的基本原理有了朦胧的认识的，但这是远远不够的，还有很多都没有讲（好吧，主要是比较懒：）），
比如feature，multiple output，activation function等等，感兴趣可以参见Michael Nielsen的[这一讲](http://neuralnetworksanddeeplearning.com/chap4.html)，
可视化地证明了神经网络对任意函数的拟合能力，非常精彩。阅读过程中可以结合PlayGround去理解。
