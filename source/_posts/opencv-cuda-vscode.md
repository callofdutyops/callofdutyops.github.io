---
title: OpenCV 4.5 CUDA版编译与VS Code配置
date: 2022-01-08 22:22:56
tags:
- OpenCV
- Tutorial
categories:
- tech
---

## CUDA版本OpenCV安装
CUDA版本的OpenCV安装主要参考[这里](https://gist.github.com/raulqf/f42c718a658cddc16f9df07ecc627be7)，写的已经很很详细了，不再赘述。

不过有两点需要注意：
1. `opencv`和`opencv_contrib`的版本可以下载最新的，比如4.5.4，`cmake`命令里注意替换版本号
2. 一般我们不希望自己编译的版本安装在系统全局，因此，`cmake`的`CMAKE_INSTALL_PREFIX`参数需要更改，我的习惯是在home里创建libs保存自定义编译的库。

最终的cmake命令示例如下：
```shell
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=~/libs/opencv-4.5.4-cuda/release \
-D WITH_TBB=ON \
-D ENABLE_FAST_MATH=1 \
-D CUDA_FAST_MATH=1 \
-D WITH_CUBLAS=1 \
-D WITH_CUDA=ON \
-D BUILD_opencv_cudacodec=OFF \
-D WITH_CUDNN=ON \
-D OPENCV_DNN_CUDA=ON \
-D CUDA_ARCH_BIN=6.1 \
-D WITH_V4L=ON \
-D WITH_QT=OFF \
-D WITH_OPENGL=ON \
-D WITH_GSTREAMER=ON \
-D OPENCV_GENERATE_PKGCONFIG=ON \
-D OPENCV_PC_FILE_NAME=opencv.pc \
-D OPENCV_ENABLE_NONFREE=ON \
-D OPENCV_PYTHON3_INSTALL_PATH=~/.virtualenvs/cv/lib/python3.8/site-packages \
-D PYTHON_EXECUTABLE=~/.virtualenvs/cv/bin/python \
-D OPENCV_EXTRA_MODULES_PATH=~/Downloads/opencv_contrib-4.5.4/modules \
-D INSTALL_PYTHON_EXAMPLES=OFF \
-D INSTALL_C_EXAMPLES=OFF \
-D BUILD_EXAMPLES=OFF ..
```

## VS Code配置
首先参考[官方文档](https://code.visualstudio.com/docs/languages/cpp)配置C++支持，现在的问题就是，如何配置让vs code能自动识别我们自定义编译的opencv版本。

新建cmake项目，CMakeList.txt文件示例如下：
```cmake
cmake_minimum_required(VERSION 2.8)

set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS "-std=c++11 -O2")

find_package(OpenCV REQUIRED NO_DEFAULT_PATH PATHS "/home/hp/libs/opencv-4.5.4-cuda/release")

include_directories(${OpenCV_INCLUDE_DIRS})
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/include")
add_executable(test test.cpp)
target_link_libraries(test ${OpenCV_LIBS})
```
注意其中的`find_package`命令，添加了`REQUIRED NO_DEFAULT_PATH`参数和具体的`PATHS`参数。

### 路径识别与自动补全
vs code settings.json添加如下配置：
```json
"C_Cpp.default.compileCommands": "${workspaceFolder}/build/compile_commands.json"
```
这样vs code就会使用cmake（编辑CMakeList文件vs code会自动生成）生成的`compile_commands.json‵文件，找到我们自定义配置的opencv路径。

### Debug
经过上面的配置，还是无法一键debug，参考[官方文档](https://github.com/microsoft/vscode-cmake-tools/blob/main/docs/debug-launch.md#debug-using-a-launchjson-file)，配置`launch.json`文件的`program`参数，示例如下：
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++ - Build and debug active file",
            "type": "cppdbg",
            "request": "launch",
            "program": "${command:cmake.launchTargetPath}",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: g++ build active file",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```
至此，vs code就可以自动识别我们自定义编译的opencv，效果如下图：
![vscode](/images/vscode.png)

happy coding~
