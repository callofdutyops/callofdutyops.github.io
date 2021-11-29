---
title: TensorFlow 源码阅读
date: 2017-03-06 02:20:15
tags:
- DeepLearning
- TensorFlow
- Tutorial
categories:
- tech
---

看完了TensorFlow官网的[两份白皮书](https://www.tensorflow.org/about/bib)后，产生了阅读源码的念头。
不过，git clone下来以后，仅从代码大小上就可以看出TF的体量之大，源码绝不是一己之力一时半会能够理解的。
几番搜索，发现网上关于源码阅读的教程确实是很少，这里[有一份不错的](http://www.cnblogs.com/yao62995/p/5773578.html)，
[这里](https://groups.google.com/a/tensorflow.org/forum/?utm_medium=email&utm_source=footer#!msg/discuss/Fp0PEvZpSP0/-5u1WcKkAgAJ)
还有google group里的讨论，为了方便读者阅读，引用于此：
```text
core/ contains the main C++ code and runtimes.

core/ops/ contains the "signatures" of the operations
core/kernels/ contains the "implementations" of the operations (including CPU and CUDA kernels)
core/framework/ contains the main abstract graph computation and other useful libraries
core/platform/ contains code that abstracts away the platform and other imported libraries (protobuf, etc)

TensorFlow relies heavily on the Eigen library for both CPU and GPU calculations.  Though some GPU kernels are implemented directly with CUDA code.

bazel builds certain C++ code using gcc/clang, and certain CUDA code (files with extension .cu.cc) with nvcc.

python/ops/ contain the core python interface
python/kernel_tests/ contain the unit tests and lots of example code
python/framework/ contains the python abstractions of graph, etc, a lot of which get serialized down to proto and/or get passed to swigged session calls.
python/platform/ is similar to the C++ platform, adding lightweight wrappers for python I/O, unit testing, etc.

contrib/*/ directories generally mimic the root tensorflow path (i.e., they have core/ops/, etc)
```