---
title: 利用TF重训练Google Inception模型
date: 2017-02-16 22:06:30
tags:
- DeepLearning
- Inception
- TensorFlow
- Tutorial
categories:
- tech
---

本篇文章中软件的下载需要科学上网支持。

主要参考资料为[这篇文章](https://www.oreilly.com/learning/dive-into-tensorflow-with-linux)，主要步骤相同，对参数，细节做了说明。

一般来讲，深度学习的模型训练需要消耗大量的计算资源，例如Google的Inception模型，使用了8张K40显卡训练了两周，普通开发者一般没有这样强大的计算资源支撑。
但是迁移学习提供了捷径，通过重训练已训练模型的最后几层，就可以将模型用于自定义图像的分类。TF提供了用于重训练的工具集。
因此，在尝试自己搭建模型并训练模型之前，应首先尝试使用重训练模型方法，这样的好处是可以先快速评估至少可以达到的准确率，对以后的模型建立，优化提供基线（baseline）。
并且，重训练生成的权重值文件可以用于TF移动端app，移动端app直接加载训练好的权重值，通过摄像头实时识别，这将在下篇博客中讲解。

这次项目中使用的是基金会提供的视网膜病变图像，数据集比较小，只有不到400张，依然达到了不错的准确率（测试集80%左右），本教程中，以开源数据集为例做示范。
开源数据集下载地址：http://download.tensorflow.org/example_images/flower_photos.tgz

首先，下载数据集
```shell
curl -O http://download.tensorflow.org/example_images/flower_photos.tgz
```
解压到某一目录下，比如`~/Downloads/flower`，可以看到，`flower_photos`文件夹中有5个分类，在重训练过程中，TF提供的工具集会将这五个分类文件夹的名称作为类别标签。
因此，若需要修改分类标签，应在这里修改文件夹的名称。

进入tensorflow源码目录，然后执行重训练Python程序（这里的参数仅仅是示例，可直接打开源码文件，底部有所有参数以及参数说明，可用于进一步调参使用），注意路径替换为自己的路径：
```shell
python3 tensorflow/examples/image_retraining/retrain.py \
--bottleneck_dir=~/Downloads/flower/bottlenecks \
--model_dir=~/Downloads/flower/inception \
--output_graph=~/Downloads/flower/retrained_graph.pb \ 
--output_labels=~/Downloads/flower/retrained_labels.txt \
--image_dir=~/Downloads/flower/flower_photos
```

参数解析：
1. bottleneck_dir：生成bottleneck文件，加快训练过程，详细解释参见[这里](https://www.tensorflow.org/tutorials/image_retraining#bottlenecks)。
2. model_dir：下载的Inception存放目录。
3. output_graph：重训练生成的权重值的存放目录。
4. output_labels：重训练生成的标签的存放目录。
5. image_dir：数据集目录。

执行Python程序后，会自动下载Inception模型，随后会生成bottleneck文件并开始重训练，此过程需要些时间。或者：TF提供了二进制重训练工具，执行以下命令编译：
```shell
bazel build --config opt tensorflow/examples/image_retraining:retrain
```

其中`--config opt`参数利用了`configure`步骤设置的指令集，可以最大化性能。参数与上面的Python程序类似，可以-h查看参数列表与用法：
```shell
bazel-bin/tensorflow/examples/image_retraining/retrain -h
```

训练完成在`output_graph`文件夹将会得到重训练好的权重值文件，接下来，编译分类器，让此分类器利用训练好的权重值执行分类任务：
```shell
bazel build tensorflow/examples/label_image:label_image
```

执行分类测试：
```shell
bazel-bin/tensorflow/examples/label_image/label_image \
--graph=~/Downloads/flower/retrained_graph.pb \
--labels=~/Downloads/flower/retrained_labels.txt \
--output_layer=final_result \
--image=~/Downloads/flower/flower_photos/8181477_8cb77d2e0f_n.jpg
```

程序所用参数应该很容易理解。
这种方法可以用来测试，如果批量对图片分类的话会很慢，因为每次执行命令都会重新加载整个权重值文件，
因此，批量分类应使用另写Python程序利用TF加载重训练的权重值建立图，然后批量分类。具体实现过程我放到了[github](https://github.com/callofdutyops/tensorflow_yxh/blob/master/predict_inception.py)上，必要的注释在源文件中。
