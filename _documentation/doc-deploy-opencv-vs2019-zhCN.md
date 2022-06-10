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

## 步骤 0: 确定这份教程适合你的情况

选择语言：[英文](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019/) / 简体中文 / [正体中文](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019-zhHK/)



这篇教程是写给那些想在自己的电脑上部署 64位 基于 CPU 的 OpenCV ，并用该框架用 Visual Studio 2019 进行应用程序开发的用户们。当然，GPU 版本 与 32 位版本 OpenCV 的配置都遵循以下步骤。

如果你需要 32 位的 OpenCV ，或者需要 OpenCV 调用 CUDA 进行加速运算，那么，需要使用 CMake 对 OpenCV 的源代码进行重新编译，再按照本文章的步骤进行配置。



## 步骤 1: 下载-安装 VS2019

- 前往 [Microsoft Visual Studio](https://visualstudio.microsoft.com) 官方网站下载并正确安装 VS 2019。



## 步骤 2：下载并安装 OpenCV 

- 前往 [OpenCV Release](https://opencv.org/releases/) 下载 OpenCV 4.5.5 或需要的版本。OpenCV 3 与 OpenCV 4 的配置方法只有些许不同。
- 运行 OpenCV 安装包，将 OpenCV 解压到 `D:\opencv\` 或者其它需要的路径地址。本文使用 `D:\opencv455\opencv\` 作为示范，下同。
- 解压后，打开系统环境变量，将 `D:\oepncv455\opencv\build\x64\vc15\bin` 添加到系统变量的 `Path` 中。



## 步骤 3：将 OpenCV 配置到 VS2019 上

**以下示意图中的路径并不准确，仅作为示意用途，不代表配置过程中的真实路径。应当按照 OpenCV 真实的安装位置进行配置。**

- 在 VS2019 中新建一个打印 Hello World 的空控制台项目。
- 在 “解决方案资源管理器” 中，在解决方案名称处右键，选择 “属性” 。
- 确定 “配置” 与 “平台” 是开发需要的配置，本文档以 Release x64 为例。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/1654590227607.png" alt="img" style="zoom:60%;" /></div>

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/16545905073982.png" alt="img" style="zoom: 67%;" /></div>

- 切换到恰当配置后，选择 "C++" - "常规" - “附加包含目录”，添加如下目录：
  `D:\opencv\build\include`  以及
  `D:\opencv\build\include\opencv2`  （如上二图所示）



<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/16545903811819.png" alt="img" style="zoom:50%;" /></div>

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/1.png" alt="img" style="zoom:67%;" /></div>

- 在 “链接器” - “常规” - “附加库目录” 中添加：
  `D:\opencv\build\x64_dnn\vc16\lib` （如上二图所示）



<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-opencv-vs2019.assets/1654590961156.png" alt="img" style="zoom:60%;" /></div>

- 在 “链接器” - “输入” - “附加依赖项” 中添加：
  `opencv_world455.lib` （如果解决方案使用 `Release` 模式）
  如果解决方案使用 `Debug` 模式，则使用 `opencv_world455d.lib` 。



此时，应当可以在这个项目中使用 OpenCV 进行开发，可以利用下面的代码，显示一张图片（图片需要执行程序前自行存入），检测 OpenCV 是否能正常工作。

```c++
#include <opencv2/opencv.hpp>
#include <iostream>
using namespace std;
using namespace cv;

int main()
{
    Mat image = imread("D:/Vs2019WorkSpace/HelloOpenCV/test.jpg");  
    //Image Path
    if (image.empty())
    {
        cout << "error";
        return -1;
    }

    imshow("OpenCV Test", image);
    waitKey(0);
    return 0;
}
```

