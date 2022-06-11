---
title: 'Compile OpenCV with CUDA using CMake'
date: 2022-06-10
permalink: /doc/2022/06/compile-opencv-cuda-cmake/
tags:
  - OpenCV
  - CUDA
  - CMake
  - Tutorials
---

**Select Language**: English / [正體中文](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake-zhHK/) / [简体中文](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake-zhCN/)



### Step 1: Install CUDA and cuDNN

Follow [This Tutorial](https://marc0cheung.github.io/doc/2022/06/install-cuda-cudnn-windows/) to install CUDA and cuDNN properly. 



### Step 2: Compile OpenCV with CUDA using CMake

**Note**: The OpenCV you download from the  [OpenCV Releases](https://opencv.org/releases/) Webpage is 64-bit CPU version of OpenCV. If you want to use 32-bit CPU version or 64-bit GPU version, you need to recompile OpenCV from its source code as this tutorial shows. However, the 32-bit GPU version of OpenCV **CANNOT** be compiled due to some unknown errors when using VS 2019 to compile. I guess that's because the CUDA or cuDNN no longer support 32-bit Application.



 - Go to [cMake Download](https://cmake.org/download/) and download the latest version of CMake.
 - Go to [OpenCV Releases](https://opencv.org/releases/) and download OpenCV 4.5.5 source code. Or you can find a folder called "sources" after you run the `.exe` programme of OpenCV Windows in the OpenCV extracted directory. 
 - Go to [OpenCV Contrib Release Tags](https://github.com/opencv/opencv_contrib/tags) and download OpenCV Contrib source code according to your OpenCV Version. The reason why we need to download this source is that OpenCV puts some new features into opencv-contrib, such as CUDA computation support. Extract the opencv-contrib.zip and puts it under `D:\opencv\sources\` 
 - new a folder named `x64_cuda_build` under `D:\opencv\build\` . Open CMake, select the source and build path accoring to your opencv directory. Click "Configure", select `Visual Studio 2019` and `x64` , click "Yes". 
 - Adjust these configurations in CMake as follows: 

```
ENABLE: WITH_CUDA
ENABLE: OPENCV_DNN_CUDA
ENABLE: OPENCV_ENABLE_NONFREE
ENABLE: CUDA_FAST_MATH
ENABLE: BUILD_opencv_world

DISABLE: ENABLE_FAST_MATH
DISABLE: BUILD_CUDA_STUBS
DISABLE: All contents after searching "python"
DISABLE: All contents after searching "java"
DISABLE: All contents after searching "test"

OPENCV_EXTRA_MODULES_PATH: Select "modules" folder under "opencv_contrib" directory
CUDA_ARCH_BIN: Choose your GPU's computing ability, e.g. For Pascal GPUs, it should be 6.1; delete other version and only keep your GPU's computing ability.

```

- Once you've configured it as described above, click the configure button again. If some of the above options don't appear at first, click the configure button for the first time and then search for them.
- **For users in China**, cMake will automatically download some content from GitHub during the build process, but this may not be possible due to the GFW issues in China. If cMake prompts you to check cMakeDownloadLog.txt during the build process, you should follow [this article](https://blog.csdn.net/painice/article/details/123347824) to resolve the problem.
- When cMake does not show any errors in red fonts, check the cMake output for the corresponding version number at CUDA, the cuDNN for `CUDNN: YES (Ver X.X.X)`, and CUDA_ARCH_BIN for the graphics card's arithmetic version. (e.g. for Pascal GPUs this version number should be `6.1; ` )
- When everything is OK with cMake, you can press the `Open Project` button, open the project with VS2019 and select `Generate` > `Batch Generate` > tick the Debug and Release versions of INSTALL and select Generate.
- After a long wait, we will see the `install` folder under the `x64_cuda_build` folder, containing the `x64` folder, which is the `.dll` file we need to use.

<br>

<br>
