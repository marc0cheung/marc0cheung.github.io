---
title: 'How To Deploy OpenCV 4.5.5 to VS2019'
date: 2022-06-07
permalink: /doc/2022/06/deploy-opencv-vs2019/
tags:
  - OpenCV
  - VS2019
  - Tutorials
  - Deploy Environment
---

## Step 0: Make sure this tutorial is suitable for you

**Select Language:** English / [Simplified Chinese](/doc/2022/06/deploy-opencv-vs2019-zhCN/) / [Traditional Chinese](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019-zhHK/)

This tutorial is for those who wants to deploy OpenCV **(64bit, CPU Computing Version)** to their computer and develop their own OpenCV programme with **Microsoft Visual Studio 2019**. 

Of course, the configurations of *OpenCV with CUDA* and *OpenCV 32-bit* are also the same as this tutorial shows, but you need to re-compile OpenCV under the specify environment and then follow this tutorial to configure OpenCV.



## Step 1: Download VS2019

- Go to [Visual Studio](https://visualstudio.microsoft.com/) Download VS2019 and properly install VS2019.



## Step 2: Download and Configure OpenCV 4.5.5

### Before Configuration, Install First

- Go to [OpenCV Release](https://opencv.org/releases/) download OpenCV 4.5.5 for Windows.
- Run `opencv-4.5.5-vc14_vc15.exe` and Extract to an empty folder as you like. 
  My OpenCV installation directory is `D:\opencv455\opencv` , the tutorials after will **keep** using this path as demonstrate. 

### Configure System Environment Variable

- Add `D:\opencv455\opencv\build\x64\vc15\bin` to `Path` of System Environment Variable. 

### Deploy OpenCV to VS2019

- Create a new console project solution with "Hello World" code in `main.cpp` in VS 2019.
- In the Solution Explorer, right click on the solution name, on the right-click menu select "Properties".
- Make sure the configuration and platform are what you need, for example, if you want to develop on `Release x64` , then you need to set the properties to `Release x64` , then configure OpenCV.
- In "C++" - "General" - "Additional Include Directories", add:
  `D:\opencv455\opencv\build\include`  and 
  `D:\opencv455\opencv\build\include\opencv2` 
- In "Linker" - "General" - "Additional library directories", add:
  `D:\opencv455\opencv\build\x64\vc15\lib`
- In "Linker" - "Input" - "Additional dependencies", add:
  `opencv_world455.lib` (if you are set the solution to `Release` Mode)
  use `opencv_world455d.lib` for `Debug` Mode instead.



## Step 3: Test if OpenCV works

- Try the following code in `main.cpp` to display an image using OpenCV to see if the framework works

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



<br>

<br>
