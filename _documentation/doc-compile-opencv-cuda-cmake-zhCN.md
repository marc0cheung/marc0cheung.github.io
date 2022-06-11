---
title: '用 CMake 编译 OpenCV with CUDA'
date: 2022-06-10
permalink: /doc/2022/06/compile-opencv-cuda-cmake-zhCN/
tags:
  - OpenCV
  - CUDA
  - CMake
  - Tutorials
  - Simplified Chinese
---

**选择语言**：[English](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake/) / [正體中文](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake-zhHK/) / 简体中文



### Step 1: 安装 CUDA

 - 确定本机安装了一张支持 CUDA 的 nVIDIA 显卡
 - 桌面上单击右键，选择 `NVIDIA控制面板` ，在菜单栏的 `帮助` > `系统信息` > `组件` 中找到 `NVCUDA.DLL`，即可看到支持的CUDA版本。
 - 前往 CUDA 官网 下载 CUDA Toolkit，可以在 [Archive of Previous CUDA Releases](https://developer.nvidia.com/cuda-toolkit-archive) 找到自己对应的版本。注意下载的版本不要超过 系统信息 中所显示的 `NVCUDA.DLL` 对应的版本。
 - 下载 `local` 版本的安装包，下载完毕后双击启动，按照屏幕上的提示进行安装，一般选择 `精简` 模式进行安装即可。
 - 安装完毕后，在系统环境变量配置中检查，是否有 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1\bin` 以及 `CUDA_PATH` 、 `CUDA_PATH_V10_1` 环境变量。一般而言，通过精简模式安装的 CUDA 均会自动添加对应的系统环境变量，无需额外配置。
 - 将 `C:\Program Files\NVIDIA Corporation\NVSMI` 文件夹添加到系统环境变量中。
 - 打开 `cmd` ，输入指令 `nvcc -V` 以及 `nvidia-smi` 进行检查。如果均有回应，则说明安装成功。
   <br><br>

### Step 2: 安装 cuDNN

 - 确定 CUDA 正确的安装在电脑上。
 - 前往 [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive) ，按照 CUDA 版本号，下载对应的 cuDNN 库。
 - 解压 cuDNN 压缩包，可以看到 `bin` 、 `include` 、 `lib` 目录。
 - 找到你安装的版本目录，打开，找到 `bin` 、 `include` 、 `lib` 目录，将 cuDNN 压缩包内对应的 **文件** 复制到 CUDA 对应的 `bin` 、 `include` 、 `lib` 目录。
   <br>
   **注意：是复制文件到bin、include、lib目录，不是复制目录。**
   <br><br>
 - 在系统变量中，添加下方两个路径：<br>
   `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2`<br>`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\lib\x64`<br>
   到此， cuDNN 安装成功。
- 检测 cuDNN 是否安装成功的方法：<br>在路径 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\extras\demo_suite` 下打开 `Windows Powershell` ，执行命令：`.\bandwidthTest.exe` 。如果出现 CUDA Bandwidth Test 对应的响应，则说明配置成功。
  <br><br>

### Step 3: 编译 OpenCV with CUDA

 - 提醒：OpenCV在官网上下载到的版本是 `64位CPU版` 。如果需要 `32位CPU版` 或 `64位GPU版` 则均需要自行编译。注意，用 cMake 与 VS2019 编译 `32位GPU版` 时，会出现报错无法编译的情况，原因未知。猜测可能是 CUDA 不再支持 32位 的应用程序了。
 - 前往 [cMake Download](https://cmake.org/download/) 下载 cMake 最新版本。
 - 前往 [OpenCV Releases](https://opencv.org/releases/) 下载 OpenCV 4.5.5 源码包（Source）。或者直接下载 Windows 版本的 `.exe` 文件，程序自解压后亦会有源码保存在 `source` 文件夹中。
 - 前往 [OpenCV Contrib Release Tags](https://github.com/opencv/opencv_contrib/tags) 下载对应版本的 OpenCV Contrib 库源代码。因为 CUDA 加速等比较新的特性，OpenCV 将其放置在 opencv_contrib 库中。将下载到的文件解压，放置到 OpenCV 的 sources 文件夹下。
 - 在 OpenCV 的 build 文件夹下新建一个 `x64_cuda_build` 文件夹，打开 cMake ，选择 source 和 build 对应的存放位置，点击 Configure 按钮，选择 VS2019 和 x64 ，确认。
 - 在 cMake 界面中，按照下列描述，调整各选项：

```
启用：WITH_CUDA
启用：OPENCV_DNN_CUDA
启用：OPENCV_ENABLE_NONFREE
启用：CUDA_FAST_MATH
启用：BUILD_opencv_world

禁用：ENABLE_FAST_MATH
禁用：BUILD_CUDA_STUBS
禁用：搜索 python 后的所有内容
禁用：搜索 java 后的所有内容
禁用：搜索 test 后的所有内容

OPENCV_EXTRA_MODULES_PATH 选择 opencv_contrib 目录下的 modules 文件夹
CUDA_ARCH_BIN：选择自己显卡的算力版本，例如 Pascal 架构显卡为 6.1; 剩下的全部去掉。

```

- 按照上述内容配置完毕后，再次点击 configure 按钮，如果刚开始上述的某些选项没有出现，请点第一次 configure 按钮后再搜索。
- **对于身处中国的用户**，cMake 在编译过程中会自动从 GitHub 上下载一些内容，此时可能由于网络原因无法下载。如果 cMake 在编译过程出现查看 cMakeDownloadLog.txt 的提示，用户应当按照 [这篇文章](https://blog.csdn.net/painice/article/details/123347824) 来解决问题。
- 当 cMake 没有任何红色错误出现后，查看 cMake 输出信息中，是否在 CUDA 处有对应的版本号，cuDNN 处是否显示 `CUDNN: YES (Ver X.X.X)` ，以及 CUDA_ARCH_BIN 处是否显示显卡的算力版本。
- 当 cMake 的一切都没有问题，即可选择 `Open Project` 按钮，用 VS2019 打开项目，选择 `生成` > `批生成` > 勾选 INSTALL 的 Debug 和 Release 版本，选择生成。
- 经过漫长的等待，我们即可在 `x64_cuda_build` 文件夹下看到 `install` 文件夹，内有 `x64` 文件夹，即我们需要用到的 `.dll` 文件。