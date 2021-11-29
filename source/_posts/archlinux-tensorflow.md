---
title: 在Manjaro Linux上安装GPU版TensorFlow
date: 2021-11-15 15:20:00
tags:
- DeepLearning
- TensorFlow
- Tutorial
categories:
- tech
---

```shell
# install nvidia driver
sudo mhwd -a pci nonfree 0300
sudo pacman -S cuda cudnn
```
