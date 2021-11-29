---
title: 西储大学轴承数据爬虫与预处理 (Case Western Reserve University Bearing Data Center crawler and preprocess)
date: 2017-11-03 22:16:32
tags:
- Data
categories:
- tech
---

想把这个写成一个系列，“深度学习算法在写paper时的应用过程——从数据到对比实验结果”，但是填坑太累，先分开写吧，最后如果能完成就整理成系列。

最近有篇关于轴承故障诊断的文章要用到轴承数据，就找到了西储大学[这个轴承的公开数据集](http://csegroups.case.edu/bearingdatacenter/home)。
关于数据集本身我就不做过多的描述了，主页说得已经很清楚。

但是问题来了，这个数据集没有提供压缩文件下载！而是用表格的形式列出的很多数据文件的集合，而且还是.mat文件，也就是MATLAB文件。
更不方便的一点是文件名都是数字，果手动下载每个文件，需要:

1. 建立不同采样频率（12k&48k）的文件夹
2. 分别下载每个分类下的每个文件
3. 3.对每个文件重新命名

这可不是个轻松加愉快的事情，本着“需要手工机械重复超过3分钟的工作应该尝试使用自动化脚本实现”这一不知道哪里看来的感觉很有道理的信念，就开始考虑如何自动化实现了。
其实最开始思路很简单，程序需要完成至少以下内容：

1. 爬取网页
2. 分析网页，获得数据的大分类（Normal&12k&48k）`category`，每个单个文件在表格中的名称&url（例如IR007\_0以及它的地址）`filename` & `url`
3. 根据2获取的`category`建立文件夹，通过`url`下载，并重命名为`filename`

整个脚本实现起来应该不难，第1, 2部分只是一个简单的获取网页+内容解析，最具有挑战性的地方在于第3步，因为可能要用多线程下载。
灵机一动，多线程下载不是有工具嘛，wget，aria2，不能放着这么好用的工具不用。 尤其是aria2，多线程下载就是一个参数的事。
因此，第3个步骤改为：

根据2获取的`category`建立文件夹，**并将`url`与`filename`保存位aria2下载列表格式**。 这样，就可以直接用shell脚本来遍历多线程下载了。

分析就到这里，程序也很清晰了，源代码在[这里](https://github.com/callofdutyops/Case-Western-Reserve-University-bearing-data-crawler)。

其中有一个Python文件和两个shell script，Python文件是用来获取`filename`，`url`等信息并生成aria2下载列表`url.txt`的， 
shell script是用来自动化遍历子文件夹下载`url.txt`的。

其中`url.txt`的格式参见aria2的[官方文档](https://aria2.github.io/manual/en/html/aria2c.html#input-file)。
