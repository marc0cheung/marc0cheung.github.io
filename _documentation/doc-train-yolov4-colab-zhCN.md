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

Google Drive 与 Google Colab 同属 Google 系列的产品，两个产品之间是支持文件交互的。所以也是一种非常好的，乃至 Google 官方推荐使用的办法。

有些用户会直接将配置好的 darknet 文件夹上传到 Google Drive 中，在 Colab 挂载了 Google Drive 后，直接在 Drive 中的 darknet 文件夹执行 `!make` 、训练神经网络 等命令。我认为这是一种不稳定的操作，因为 Google Drive 在训练过程中有断开链接的可能，一旦断开，训练将直接终止，带来麻烦。

所以，我更加推荐在挂载 Drive 后，多执行一条 cp 指令，将 darknet 文件夹复制到 Colab 的存储空间中，这样训练将更加稳定。

（当然有些人是拿来备份的）

