---
title: 新手向的TensorFlow学习之路
date: 2017-03-28 23:12:36
tags:
- DeepLearning
- TensorFlow
- Tutorial
categories:
- tech
---

2022更新：距离初版答案编写已经过去6年，就算是第二版的答案，很多内容也已经过时或者失效。我本人也很久不从事这个领域了，因此这次更新我会尽自己所能保持内容的有效性，还请读者辩证阅读。

> 阅读目的声明
> 
> 发现很多人看这个回答并不是要学习TensorFlow本身，而是想高效入门并达到初级机器学习/深度学习实战水平。如果你是这类读者，那么直接看[机器学习实战](https://book.douban.com/subject/35218199/)这本书（个人强烈推荐，属于读一本少一本的宝藏级书籍），并停止向下阅读本回答。如果你想深入了解、学习、使用TensorFlow，那么接着往下看。

TL;DR：有同学反应资源太多不知道从何看起，或者有点基础了想快速上手，因此就直接把几个比较好的教程放在这里，后面的内容作为参考。
1. TensorFlow 实战Google深度学习框架 第2版（喜欢阅读中文资料的可以先看这本书）
2. 两本书籍：TensorFlow Machine Learning Cookbook以及TensorFlow for Machine Intelligence，这两本网络均有pdf版本，请自行搜索
3. Udacity公开课，深度学习
4. 《三日入门TensorFlow》，直接搜索即可找到资源
5. （推荐参考）[Awesome TensorFlow](https://github.com/jtoy/awesome-tensorflow)

> 前言
> 其实TensorFlow本身仅仅是一个分布式的高性能计算框架，想要用TF做深度学习，仅仅学习这个框架本身是没有太大意义的。因此应该将TF看作技术路线中的一个核心点，去掌握整个开发所需要的必要技术，知识。尤其是深度学习的基本原理，这对日后搭建模型，模型调参以至提出新的模型都是极其有用的。

## 不是前提的前提--英语阅读【公共基础】
需要有扎实的英语阅读能力，其实与其说扎实，不如说是能**沉下心**看英语。
其实我最开始看这些大段英文文档的时候，我是拒绝的，后来发现真的，这是绕不过去的坎，再后来坚持背英语单词（推荐扇贝单词）加上耐着性子读好了很多。
至于原因，为什么读英文，那是因为不仅很多优秀的文档有且仅有英文的，更是很多国内的开发者也会用英文写。
而且，如果对于做学术的人，那么英语作为通用语言更是要熟练掌握。但是我尽量每个部分教程都给出一份中文的，尽量保证在不想看英文的情况下也有不错的指导。

## 科学上网【公共基础】
这是个不是技术的技术，不做过多解释
> 在这里插一句，本来是不想说这些的，有推销嫌疑，但是有很多人问这个问题，有哪些推荐的梯子。
> 我是用的是[Digitalocean](https://m.do.co/c/ee79ae347c9f)（这是我的推荐链接 ，使用的话你我都可以获得奖励）搭建服务器。
> 稳定性不错，$5/月，1T流量，在服务器上跑了一个shadowsocks的服务端

## Github【公共基础】
这是个开源程序的网站，Linux内核就在这个网站托管。Github的核心是Git，一种版本控制系统，已经逐渐取代SVN。
这个网站托管了很多高质量的或者说世界顶尖的开源项目，比如TensorFlow。学习一下这个网站如何使用，注册个账号，学习一下Git的使用方法。
这个网站有自己的tutorial和guide。而且后期自己的项目管理多半也会用到git，所以，早点学习肯定是有好处的。
### 【fast peace（即简略的快节奏教程，下同）】
1. [Git 参考手册（推荐）](http://gitref.justjavac.com/)
2. [git - the simple guide](http://rogerdudler.github.io/git-guide/)
3. [Git - Quick Guide](https://www.tutorialspoint.com/git/git_quick_guide.htm)

### 【hard way（即大而全的完整或者正式教程，下同）】：
1. [Pro Git与Git的参考文档](https://git-scm.com/doc)
2. [Git - Tutorial](http://www.vogella.com/tutorials/Git/article.html)
3. [Git Tutorial](https://www.tutorialspoint.com/git/)

## Python【公共基础】
这是一种非常流行的脚本语言，庞大的第三方库可以快速写出短小容易理解的代码，而且也是TensorFlow的推荐开发语言。
不仅仅是对于TensorFlow这么语言很重要，对于整个机器学习，应用层面放眼望去基本就是Python与R的天下了，而R更偏向与统计学领域，
深度学习Python简直是红透的半边天，当然我是针对初学者，如果已经掌握了其他语言，例如Java，C++，那么其实理论上而言可以不用掌握Python，
选择自己熟悉的语言的API即可。但是对于TensorFlow，只有Python的API最全面，文档社区也最完善（截至2017年3月），
当然随着TensorFlow的发展都会完善，可是现在就要用呀：）。而且，最重要的是，对于已经掌握其他语言的人来说，学习Python不会花费太久的。
并且，Pytohn写起来短小精悍，易于理解，很适合这种用来搭架子调用框架而且不怎么需要考虑效率的场景（一般来讲，最需要效率的部分框架会用C++实现，Python仅仅是作为顶层去调用）。
另外，推荐学习Python3，这才是Python的未来，2.7的支持在2020年会停止。而且，后面会知道的，对于Python2.7向3的支持很多情况仅仅是import几个future包。
### 【fast peace】
1. [Tutorial - Learn Python in 10 minutes](https://www.stavros.io/tutorials/python/)
2. [Python Quick Guide](https://www.tutorialspoint.com/python/python_quick_guide.htm)
3. [Google's Python Class](https://developers.google.com/edu/python/)

### 【hard way】：
1. [廖雪峰的Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)
2. [The Python Tutorial](https://docs.python.org/3/tutorial/index.html)

### Linux【公共基础】
TensorFlow的主要运行平台之一就是Linux，但是正式版对Windows的支持日趋完善，真的没时间学习Linux平台可以先在Windows上运行TensorFlow。
不过，学习Linux真的用不了多久（当然是指做开发环境日常日用，立志做系统管理员还是要下一番功夫的）。推荐Ubuntu 16.04 LTS，
这不仅是“新手友好”的发行版，也是Google很多产品的官方支持版本，官方支持就会带来很多便捷以及少一些”坑“。
LTS（长期支持版本）的加成保证了系统的稳定（稳定不仅指运行稳定，更是指软件环境，例如python不会突然默认变成3.6，gcc不会突然就默认变成6）。
插一句，之所以这么说，是因为以前用的是Arch，装了TensorFlow一直跑的很开心，直到有天突然发现跑不起来了，原来是Arch升级了一下Python的默认版本变成了3.6了XD。 
### 【fast peace】：
1. [我的知乎回答](https://www.zhihu.com/question/52958265/answer/132890313)

### 【hard way】：
1. [UNIX Tutorial for Beginners](http://www.ee.surrey.ac.uk/Teaching/Unix/)
2. [Linux Tutorial](http://ryanstutorials.net/linuxtutorial/)
3. [Linux工具快速教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/)
4. [Learn Online in 7 days](https://www.guru99.com/unix-linux-tutorial.html)

## 深度学习--概念与基础
### 【fast peace】（偏向概念介绍，introduction）
1. [深度学习wiki](https://zh.wikipedia.org/zh-hans/%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0)

### 【hard way】（偏向概念解释，数学推导，基本原理）
两本经典中的经典书籍：第一本倾向于直观性的解释，第二本就更加理论了。 
1. [Deep Learning](http://www.deeplearningbook.org/)
2. [Neural Networks and Deep Learning](http://neuralnetworksanddeeplearning.com/) 代码应用向的，使用Theano从零搭建各种常见的网络结构

## Python常用科学计算库【公共基础】
虽然说是公共基础部分，但是系统学习这几个库，也不是一件容易的事，而且很耗时间。因此还是遵循够用即可的原则。
但是还是想列在这里，因为后来发现如果这几个库掌握的比较熟练，数据预处理后处理都能极大地节省时间，当你还在苦苦想如何设计预处理方法的时候，
其实去查一查手册，大概率在这几个库里已经写好了。
这几个库的官方文档都很优秀，比如Pandas的[10 Minutes to pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/10min.html)。因此，闲余时间多看看官方tutorial即可。
1. Numpy
2. Sklearn
3. Matplotlib
4. Pandas

## TensorFlow
### 【fast peace】（偏向应用性质，到手即用，pretrained-models，tflearn，keras等）
1. [TensorFlow白皮书](https://www.tensorflow.org/about/bib)，对TensorFlow的整体有个把握或者说印象是很有必要的，对后期的“图编程”，优化，都很有启发
2. Keras，High Level API，目前已经集成进TensorFlow了

### 【hard way】（偏向理论性质，调用基本API手写，自定义layer，自定义kernel，自定义optimizer等）
1. [官网教程](https://www.tensorflow.org/tutorials)
2. [Google的Udacity课程](https://cn.udacity.com/course/deep-learning--ud730)

## 论文阅读路线图
### 【hard way】
github上有个很有名的repo，已经过万stars，从事深度学习科研者的必读清单。
1. [Deep Learning papers reading roadmap](https://github.com/songrotek/Deep-Learning-Papers-Reading-Roadmap)

## 相关博客等其他资源
### 教程、资源
1. [CS231n: Convolutional Neural Networks for Visual Recognition](http://cs231n.stanford.edu/)
2. [CS224n: Natural Language Processing with Deep Learning](http://web.stanford.edu/class/cs224n/)
3. [UFLDL Tutorial](http://deeplearning.stanford.edu/tutorial/)
4. [First Contact With TensorFlow](https://torres.ai/first-contact-with-tensorflow/)
5. [Practical Deep Learning for Coders](https://course.fast.ai/)（一个不错的视频教程）
6. [DeepLearning4J](https://deeplearning4j.konduit.ai/multi-project/tutorials/beginners)（虽然是一个面向Java的深度学习框架，但是文档很好，通用性也很强）
7. [arxiv-sanity](https://arxiv-sanity-lite.com/)
### 优秀个人博客
1. [colah](http://colah.github.io/)（强烈推荐，写了很多晦涩概念可视化的文章）
2. [Chris McCormick](http://mccormickml.com/)
3. [Daniil](http://warmspringwinds.github.io/)
4. [ireneli](https://ireneli.eu/)
5. [Hackery](https://acko.net/)
