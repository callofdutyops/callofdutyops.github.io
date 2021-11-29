---
title: 浅谈伪计数 pseudo counts
date: 2017-11-23 01:00:25
tags:
- Math
- Tutorial
categories:
- tech
---

最近看自然语言处理词频统计的时候，看到的一种方法，小记于此。

伪计数可以看做是一种平滑，由拉普拉斯提出，也称为拉普拉斯平滑。伪计数估计量的具体公式是：

{% mathjax %}\hat\theta_i = \frac{x_i + \alpha}{N + \alpha d} \quad (i =1,...,d){% endmathjax %}
其中{% mathjax %}\hat\theta_i{% endmathjax %}是估计量，{% mathjax %}x_i{% endmathjax %}是观测到的事件分类，{% mathjax %}\alpha{% endmathjax %}是平滑参数（有些学者认为应该是1，不过一般都比较小，比如取0.5），{% mathjax %}N{% endmathjax %}是观测事件总数，{% mathjax %}d{% endmathjax %}是观测值的可能分类，例如二分类问题中{% mathjax %}d=2{% endmathjax %}。

那么这个公式是什么意思呢？具体地说，为什么要这么规定这种平滑？那是因为这种平滑的好处是它的值在经验估计：{% mathjax %}\frac{x_i}{N}{% endmathjax %}与均匀分布概率{% mathjax %}\frac{1}{d}{% endmathjax %}之间。也就是很好地完成了从事实发生概率到随机发生概率的平滑任务。

再具体一点是什么意思呢？那么我觉得就要从它的来历说起了。历史上，拉普拉斯试图估计明天太阳升起的可能性的时候，提出了这个概念。
大家都知道，太阳当然会升起啊！不升起来的概率（或者说观测到不升起来的次数）为0。等等，那可不一定，至少拉普拉斯不这么认为，
他甚至认为日出的前一秒，也不能确定能不能观测到太阳升起。当然，这不是抬杠，仔细想想确实是这个道理，毕竟谁也不知道太阳会不会被一个极小概率的事件毁灭。

那么，这个概率到底是多少？这个概率怎么计算出来既可以满足事实（姑且叫做“事实概率”），又可以满足幻想（姑且叫做“幻想概率”）。答案是：平滑。

比如，从我开始观测，一共是{% mathjax %}N=98{% endmathjax %}次观测，其中有：

{% mathjax %}x_{i}=0, i=no\ sunraise, i\ in\ \{sunraise,no\ sunraise\}{% endmathjax %}

取{% mathjax %}\alpha = 1{% endmathjax %}从{% mathjax %}i{% endmathjax %}的集合可以知道，{% mathjax %}d=2{% endmathjax %}
那么{% mathjax %}\hat\theta = 0.01{% endmathjax %}很接近事实（事实概率为0），但又有那么点发生幻想的几率（幻想概率为0.01）。 随着观测的进行，{% mathjax %}N{% endmathjax %}越来越大，
而{% mathjax %}x_i,\ i=no\ sunraise{% endmathjax %}却没什么长进，那么幻想概率会越来越小但又不是不会发生。

可以看出，伪计数很好的实践了“虽然我观测没发生，但是也不否认发生的可能性，只是发生的可能性随着我观测我觉得越来越小而已”这一贝叶斯派的思想。其实，这就是一种从贝叶斯角度看概率的实例。

参考资料：[Additive smoothing](https://en.wikipedia.org/wiki/Additive_smoothing)
