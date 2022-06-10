---
title: '利用 Google Colab 训练自己的 YOLOv4 权重'
date: 2022-06-09
permalink: /doc/2022/06/train-yolov4-colab-zhCN/
tags:
  - YOLOv4
  - YOLOv4 Weights
  - Tutorials
  - Google Colab
  - Simplified Chinese
---

<aside>
💡 Use this documentation to describe the steps engineers should follow to deploy.
</aside>



**选择语言**：[English](https://marc0cheung.github.io/doc/2022/06/train-yolov4-colab/) / [正体中文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-colab-zhHK/) / 简体中文



> Colaboratory 是一个 Google 研究项目，旨在帮助传播机器学习培训和研究成果。它是一个 Jupyter 笔记本环境，不需要进行任何设置就可以使用，并且完全在云端运行。
>
> Colaboratory 笔记本存储在 Google 云端硬盘中，并且可以共享，就如同您使用 Google 文档或表格一样。Colaboratory 可免费使用。
>
> 利用Colaboratory，可以方便的使用Keras, TensorFlow, PyTorch, OpenCV等框架进行深度学习应用的开发。

众所周知，GPU 是深度学习开发的重要硬件需要，而 Google Colab 相当于给我们提供了一个功能强大，使用方便，最重要是**免费**的GPU资源！



本教程将主要关注在 Google Colab 层面的使用和操作，关于 YOLOv4 训练前的文件准备工作，请参阅 [训练自己的 YOLOv4 权重](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights-zhCN/) 。



## 与 Google Colab 进行文件交互

Google Colab 的文件存储机制类似于 RAM —— 断电后即清空。当用户的运行时（Run Time）发生改变，或者用户将浏览器关闭了，那么用户上传的文件将全部丢失。同时，由于 Colab 的文件管理用户界面交互体验奇差无比，仅仅适用于少数小体积文件的传输。所以，针对训练深度学习网络文件多且复杂的情况，必须要有一种高效、稳定的文件传输方式。

经过摸索，发现有两种方法是比较好用的：

- 利用 GitHub 与 Google Colab 进行文件交互（适用于文件较少的情况）
- 利用 Google Drive 与 Google Colab 进行文件交互（适用于大部分情况）

具体的操作方法如下。



### 利用 GitHub 进行文件传输

- 登录自己的 GitHub 账号后，将 [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) Fork 到自己的 GitHub 账户下。

- 将训练图片素材、`.cfg` 文件、`.data` 文件、`.names` 文件、`.txt` 文件等上传到自己 GitHub 账户中 Fork 来的 Repo 中。

- 打开 Google Colab，切换运行时到 GPU 版本，利用如下命令检查是否获得了 GPU 运行时：

  ```python
  !nvidia-smi
  !/usr/local/cuda/bin/nvcc --version
  ```

- 在 ./content/ 即默认目录下，执行如下命令将准备好的 Repo git 到 Colab 的 存储空间中：

  ```python
  !git clone https://github.com/{username}/darknet.git
  ```

- 利用 `!ls` 指令，检查 ./content 目录下是否有如下三个文件夹：

  ```bash
  darknet-master	drive  sample_data
  ```

- 至此，完成了利用 GitHub 对 Colab 的文件传输。

由于 Google Colab 服务器架设在美国，它执行 git clone 命令的速度是非常快的，但它也有一定的缺点：

- **GitHub 的文件上传速度并不快**，遇到文件较多的情况下有不稳定的情况出现。
- **GitHub Webpage 一次只能上传 100 个文件**。在训练网络时，数百上千张图片如果分批上传到 GitHub，操作过程非常繁琐。
- **GitHub 无法上传空白文本文件**。在训练过程中，对负样本（即图片中没有任何目标的情况）进行打标，会得到一个空白的文档，但 GitHub 并不支持空文档的上传，所以进而无法将负样本传入网络中训练。



### 利用 Google Drive 进行文件传输

Google Drive 与 Google Colab 同属 Google 系列的产品，两个产品之间是支持文件交互的。所以也是一种非常好的，乃至 Google 官方推荐使用的办法。具体操作步骤如下：

- 首先配置好 darknet 文件夹。若不知如何配置，可参见本教程末尾【darknet 文件夹训练前内容存放指南】

- 将 darknet 文件夹上传到 Google Drive 中。

- 打开 Google Colab，新建项目，并切换到带 GPU 的运行时。

- 执行以下命令，挂载 Google Drive: 

  ```python
  from google.colab import drive
  drive.amount(/drive/MyDrive)
  ```

- 执行 `cp -r` 指令，将 Google Drive 中的 darknet 文件夹拷贝到 ./content/ 目录中：

  ```python
  !cp -r ./drive/MyDrive/darknet/ ./content/
  ```

  

- 用 `!ls` 指令确认 ./content/ 目录下是否有如下三个文件夹：

  ```python
  darknet  drive  simple
  ```

- 至此，就已经成功通过 Google Drive 与 Google Colab 实现文件传输。



有些用户会直接将配置好的 darknet 文件夹上传到 Google Drive 中，在 Colab 挂载了 Google Drive 后，直接在 Drive 中的 darknet 文件夹执行 `!make` 、训练神经网络 等命令。我认为这是一种不稳定的操作，因为 Google Drive 在训练过程中有断开链接的可能，一旦断开，训练将直接终止，带来麻烦。

所以，我更加推荐在挂载 Drive 后，多执行一条 cp 指令，将 darknet 文件夹复制到 Colab 的存储空间中，这样训练将更加稳定。

当然，在有些训练时长比较久的场景下，有部分用户直接在 Google Drive 路径下进行训练，是有用作备份的意图的。这种情况下，由于 Google Colab 本身会对长时间保持连接的用户自动断开，用这种方法保持连接也是一个好选择。唯需要注意保证外网连接正常，尽量避免 Google Drive 与 Colab 断开连接。



## 用 Google Colab 开始训练

文件传输完毕后，就可以开始训练了，由于 Colab 已经配置好了 CUDA、cuDNN 和 OpenCV 环境，所以只需要调整 Makefile 设置，编译 darknet 后，直接开始训练就可以了。

- 使用 `!cd darknet` 命令，进入 darknet 文件夹

- 运行如下代码，修改 Makefile ，使其调用 CUDA、cuDNN 以及 OpenCV 进行训练：

  ```python
  !
  !
  !
  ```

- 执行 `!make` 指令对 darknet 进行编译

- 编译成功后，使用训练指令进行训练即可，如下：

  ```python
  ./darknet detector train cfg/xxx.cfg cfg/xxx.data yolov4-tiny.conv.29 -dont_show
  
  # 由于是在服务器上训练，所以使用 -dont_show 指令避免出错。
  ```

  



## darknet 文件夹训练前内容存放指南

在训练之前，需要将所有文件都准备妥当，一起上传到 Colab 中进行训练。尽量能做到一次传输即可开始训练，避免文件重复传输导致隐藏的错误发生。

1. 从  [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) 下载 darknet 源码后，解压出来得到一个名为 `darknet-master` 的文件夹。将其重命名为 `darknet` 。
2. 由于是在服务器上训练，所以不需要像 Windows 一样将文件放到 `darknet\build\darknet\x64` 下，而是直接放到 `./darknet/` 下即可
3. 将存有训练素材图片的 `test` 和 `train` 文件夹放入 `./darknet/data/` 下。
4. 将存有训练素材图片**路径**的 `test.txt` 与 `train.txt` 文件也放入 `./darknet/data/` 下。
5. 将 `remote.names` 文件也放入 `./darknet/data/` 下。
6. 将 `remote.cfg` 与 `remote.data` 放入 `./darknet/cfg/` 下。
7. 将 COCO 预训练模型 `yolov4-tiny.conv.29` 放入 `./darknet/` 下。



**文件存储路径预览如下**：

```
./darknet/
|_____ data
|      |_____ test
|      |      |_____ 1.png
|      |      |_____ 1.txt
|      |      |_____ ...
|      |      |_____ n.png
|      |      |_____ n.txt
|      |_____ train
|      |      |_____ 3.png
|      |      |_____ 3.txt
|      |      |_____ ...
|      |      |_____ x.png
|      |      |_____ x.txt
|      |_____ remote.names
|      |_____ train.txt
|      |_____ test.txt
|_____ cfg
|      |_____ remote.cfg
|      |_____ remote.data
|
|_____ yolov4-tiny.conv.29
```



