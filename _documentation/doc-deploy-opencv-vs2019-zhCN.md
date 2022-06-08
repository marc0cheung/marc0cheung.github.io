---
title: '在 VS2019 项目上部署 OpenCV'
date: 2022-06-07
permalink: /doc/2022/06/deploy-opencv-vs2019-zhCN/
tags:
  - OpenCV
  - VS2019
  - Tutorials
  - Deploy Environment
  - Simplified Chinese
---

## 在 VS2019 项目上部署 OpenCV （以空控制台项目为例）

## 步骤 0: 确定这份教程适合你的情况

选择语言：[英文](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019/) / 简体中文 / [正体中文](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019-zhHK/)



这篇教程是写给那些想在自己的电脑上部署 64位 基于 CPU 的 OpenCV ，并用该框架用 Visual Studio 2019 进行应用程序开发的用户们。

如果你需要 32 位的 OpenCV ，或者需要 OpenCV 调用 CUDA 进行加速运算，那么，需要使用 CMake 对 OpenCV 的源代码进行重新编译。





**以下示意图中的路径并不准确，仅作为示意用途，不代表配置过程中的真实路径。应当按照 OpenCV 真实的安装位置进行配置。**

- 在 VS2019 中新建一个打印 Hello World 的空控制台项目。
- 在 “解决方案资源管理器” 中，在解决方案名称处右键，选择 “属性” 。
- 确定 “配置” 与 “平台” 是开发需要的配置，本文档以 Release x64 为例。

<img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/1654590227607.png" alt="img" style="zoom:60%;" />

<img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/16545905073982.png" alt="img" style="zoom: 67%;" />

- 切换到恰当配置后，选择 "C++" - "常规" - “附加包含目录”，添加如下目录：
  `D:\opencv\build\include`  以及
  `D:\opencv\build\include\opencv2`  （如上二图所示）



<img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/16545903811819.png" alt="img" style="zoom:50%;" />

<img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/1.png" alt="img" style="zoom:67%;" />

- 在 “链接器” - “常规” - “附加库目录” 中添加：
  `D:\opencv\build\x64_dnn\vc16\lib` （如上二图所示）



<img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/1654590961156.png" alt="img" style="zoom:60%;" />

- 在 “链接器” - “输入” - “附加依赖项” 中添加：
  `opencv_world455.lib` （如果解决方案使用 `Release` 模式）
  如果解决方案使用 `Debug` 模式，则使用 `opencv_world455d.lib` 。