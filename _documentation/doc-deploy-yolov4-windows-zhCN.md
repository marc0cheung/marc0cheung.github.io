---
title: '如何在 Windows 10 上部署 YOLOv4 检测网络（基于Darknet）'
date: 2022-06-08
permalink: /doc/2022/06/deploy-yolov4-windows-zhCN/
tags:
  - YOLOv4
  - Darknet
  - Tutorials
  - Windows
  - Simplified Chinese
---

<aside>
💡 Use this documentation to describe the steps engineers should follow to deploy.
</aside>
**选择语言**：[English](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows/) / [正體中文](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows-zhHK/) / 简体中文



### 1. 前往 GitHub 下载 YOLO v4 源代码

前往 Alexey 的 GitHub ：[https://github.com/AlexeyAB/darknet/](https://github.com/AlexeyAB/darknet/)

点击绿色的 Code 按钮，选择 Download ZIP，保存到所需的位置。



### 2. 安装 LabelImg 打标软件

确保本机装有 Python 环境（推荐使用 Python 3.8.10）并确保 pip 可用

`pip安装:` `pip install labelImg`

`cmd下启动:labelimg` 即可完成安装。

Reference Link：****[labelimg使用简介](https://blog.csdn.net/m0_46653437/article/details/109669646)****



### 3. 安装MSVC 2019

前往 VS 官网下载即可。



### 4.（如有GPU）下载 Nvidia 驱动、CUDA、cuDNN

参考文章1：[https://zhuanlan.zhihu.com/p/416712347](https://zhuanlan.zhihu.com/p/416712347)

参考文章2：[https://blog.csdn.net/weixin_39640773/article/details/112843020](https://blog.csdn.net/weixin_39640773/article/details/112843020)

- 下载对应显卡型号的驱动，安装完毕后，把 `C:\Program Files\NVIDIA Corporation\NVSMI` 添加到系统的环境变量 `Path` 中。在 cmd 中输入： `nvidia-smi` ，如果能成功看到显卡信息，则说明驱动安装成功。
- 在显卡驱动中查询 CUDA 版本，如下图所示。注意，官网上所选择下载的版本不能高于显卡驱动里显示的那个版本号，比如下图就不能高于11.4。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled.png" alt="Untitled" width="750px" /></div>

- CUDA Toolkit 下载地址：[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)
完成下载后安装，**建议选择默认路径、“精简” 模式安装**。
安装时勾选 `Visual Studio Intergration` 。安装完毕后，配置 CUDA 环境变量。
- **2022年6月8日补充**：如果选用 ”精简“ 安装模式，所有的环境变量会自动配置完毕，无需手动执行下列引用块中所提及的配置。

> 计算机上点右键，打开属性->高级系统设置->环境变量，可以看到系统中多了CUDA_PATH和 CUDA_PATH_V10_2两个环境变量。
> 
> 
> 接下来，还要在系统中添加以下几个环境变量(默认安装路径):
> 
> C:\ProgramData\NVIDIA Corporation\CUDA Samples\v10.2
> 
> CUDA_SDK_PATH = C:\ProgramData\NVIDIA Corporation\CUDA Samples\v10.2
> 
> CUDA_LIB_PATH = %CUDA_PATH%\lib\x64
> 
> CUDA_BIN_PATH = %CUDA_PATH%\bin
> 
> CUDA_SDK_BIN_PATH = %CUDA_SDK_PATH%\bin\win64
> 
> CUDA_SDK_LIB_PATH = %CUDA_SDK_PATH%\common\lib\x64
> 
> 然后，在系统变量 Path 的末尾添加： %CUDA_LIB_PATH%;%CUDA_BIN_PATH%;%CUDA_SDK_LIB_PATH%;%CUDA_SDK_BIN_PATH%;
> 
> 再添加如下5条(默认安装路径)：
> 
> C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\lib\x64
> 
> C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\include
> 
> C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\extras\CUPTI\lib64
> 
> C:\ProgramData\NVIDIA Corporation\CUDA Samples\v10.2\bin\win64
> 
> C:\ProgramData\NVIDIA Corporation\CUDA Samples\v10.2\common\lib\x64
> 

CUDA安装完毕后，在 cmd 中输入： `nvcc -V` 指令进行测试。

- cuDNN（用于深层神经网络的 GPU 加速库）
下载地址（需注册 Nvidia 账号）：[https://developer.nvidia.com/rdp/cudnn-download](https://developer.nvidia.com/rdp/cudnn-download)
cuDNN的版本需要与刚才下载的CUDA版本对应。选择对应CUDA版本分支下的cuDNN Library for Windows (x86)，如下图所示

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled 1.png" alt="Untitled" width="1000px" /></div>

⚠️⚠️⚠️ **具体安装流程请参考：** [https://zhuanlan.zhihu.com/p/416712347](https://zhuanlan.zhihu.com/p/416712347)



### 5. 安装 OpenCV 4.5.5

OpenCV Official Download Link: [Link](https://opencv.org/releases/)

解压后设置环境变量：在系统环境变量中添加一个名为 `OpenCV_DIR` 的变量（如下圖所示），Value 设置为 `D:\opencv\build （取决于安装路径）`

在系统环境变量的 `Path` 中，添加路径 `D:\opencv455\opencv\build\x64\vc15\bin` ，这里的路径为 OpenCV 所解压到的目录。如果需要 32位 的 OpenCV，或者 未来在 VS2019 开发中需要用到 OpenCV with CUDA ，则需要自行通过 CMake 编译。这一点在另一篇说明文档中将会提到。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled%202.png" alt="Untitled" width="800px" /></div>



### 6. 安装 CMake 并用 CMake 编译 Darknet

- 事先是否要改 Makefile ？Linux才要，Windows直接在cmake里面修改即可。

CMake GUI: `Windows win64-x64 Installer`  [https://cmake.org/download/](https://cmake.org/download/)

选择最新版本安装，安装完毕后启动CMAKE GUI，按下图流程操作

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled%203.png" alt="Untitled" width="700px" /></div>

CMAKE提示：You might need to add D:\opencv\build\x64\vc15\bin to your PATH to be able to run your applications.

完成 Configure 流程之后 CMake 会自动进行编译，选择 Open Project ， VS 打开之后选择 Release X64 ，重新生成解决方案。编译完毕后可以在 `darknet\Release` 里面看到编译出来的 `darknet.exe` ，将这个文件拷贝到 `darknet\build\darknet\x64` 里面。

将 `…\opencv\build\x64\vc14\bin` 下的两个 `opencv_world455.dll` 和  `opencv_ffmpeg455_64.dll`  文件复制到上述文件夹内。

（如果有GPU）将 cuDNN 中的 `cudnn64_7.dll` 复制到上述文件夹。



## Useful Tools:

PIP清华源：pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package

安装参考教程1：[Link](https://www.cnblogs.com/aiqinger/p/14823763.html)



### N. 实现目标检测

以上已经完成了所有环境的配置，可以使用yolov4.weights实现目标检测了，方法如下：

首先在 GitHub 上下载 `yolov4.weights` 文件，将这个文件拷贝到 `darknet\build\darknet\x64` 里面。

在D:\darknet\build\darknet\x64\ 目录下

CMD执行：

```jsx
darknet.exe detector test cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote1.jpg
```

对图像进行目标检测；

CMD执行：

```bash
darknet.exe detector demo cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote_video.mp4
```

对视频进行目标检测。

#### Powershell Command Line (Non-GPU Version):

Reference: ****[YOLO V4系列学习（六）DARKNET代码的使用小技巧](https://www.freesion.com/article/1880953679/)****



##### YOLO v3 Tiny

Video Detection via YOLO v3 Tiny (Avg FPS around 4) with low recognition rate

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov3-tiny.cfg yolov3-tiny.weights data\remote\remote.mp4
```

Image Detection via YOLO v3 Tiny (Relatively fast speed but very low recognition rate)

```bash
.\darknet.exe detector test cfg\coco.data cfg\yolov3-tiny.cfg yolov3-tiny.weights data\remote\remote1.jpg
```

Real-Time Detection using local USB Camera based on YOLOv3 Tiny （识别率像个傻子）

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov3-tiny.cfg yolov3-tiny.weights
```

##### YOLO v4 Tiny

Image Detection via YOLO v4 Tiny (1~2 sec per Image, quite fast)

```bash
.\darknet.exe detector test cfg\coco.data cfg\yolov4-tiny.cfg yolov4-tiny.weights data\remote\remote1.jpg
```

Video Detection via YOLO v4 Tiny (AVG FPS around 5.9, quite good)

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov4-tiny.cfg yolov4-tiny.weights data\remote\remote.mp4
```

Real-Time Detection using local USB Camera based on YOLOv4 Tiny

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov4-tiny.cfg yolov4-tiny.weights
```

##### YOLO v4 Standard Version

Image Detection via YOLO v4 ( Slow but super high recognition rate )

```bash
.\darknet.exe detector test cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote\remote1.jpg
```

Video Detection via YOLO v4 (Avg FPS around 0.2, totally unuseable)

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote\remote.mp4
```

Real-Time Detection using local USB Camera based on YOLOv4 (AVG FPS 0.3, FOR GOD SAKE!)

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov4.cfg yolov4.weights
```



## 总结

实际上，如果用 Python 或者 C++ 版本的软件实现检测，完全不需要配置 Darknet 环境（參考[我的項目](https://github.com/marc0cheung/YOLOv4_Detection_dnnOpenCV)）。直接将训练好的网络文件传进 Python 或 C++ 程序中即可。Python 程序是“开箱即用”的，但 C++ 程序还需要在 VS2019 中配置好 OpenCV 运行环境方可运行。

训练环境是必须要使用 Darknet 的。所以，在这里写 Darknet 的配置方法，主要是为了日后配置显卡后，可以在自己的计算机上进行训练深度学习网络。先配置好 Darknet ，再训练网络，得到权重，就可以传进 C++ 或 Python 的实时检测程序中使用了。