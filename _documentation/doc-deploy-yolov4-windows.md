---
title: 'Deploy YOLOv4 (Darknet) on Windows'
date: 2022-06-08
permalink: /doc/2022/06/deploy-yolov4-windows/
tags:
  - YOLOv4
  - Darknet
  - Tutorials
  - Windows
---

<aside>
💡 Use this documentation to describe the steps engineers should follow to deploy.
</aside>



**Select Language**: English / [Traditional Chinese](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows-zhHK/) / [Simplified Chinese](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows-zhCN/)



### 1. Donwload YOLOv4 Source Code from GitHub

Go to [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) to Download the Latest Source Code of YOLOv4, extract the .zip file and place the folder to the destination you want. 



### 2. Install LabelImg (if needed)

- Ensure Python Environment is properly installed to your PC. Try `python` in `cmd` to check whether there is a Python Environment already. 

- Use `pip install labelimg` in cmd.
- Run LabelImg: use `labelimg` in `cmd`



### 3. Install MSVC 2019

Go to [Microsoft Visual Studio](https://visualstudio.microsoft.com/) to download, and follow the screen prompt to install MSVC 2019 properly.



### 4. Download & Install CUDA (If you want to use GPU to run Darknet)

- Right Click on Windows desktop and select **NVIDIA Control Panel** 
- Find `NVCUDA.DLL` in the menu bar under `Help` > `System Information` > `Components` to see the supported CUDA version of your GPU, which is 10.1 for GT 1030.
- Download CUDA from [CUDA Archive](https://developer.nvidia.com/cuda-toolkit-archive) , be careful **NOT** to download a version higher than the version number you find from `NVCUDA.DLL`.
- Run the local installation that you just download from CUDA Toolkit Website
- During installation, select the **"Lite"** installation mode. Installing CUDA will automatically install the appropriate drivers for the graphics card, so it is normal for the screen to flicker during installation.
- After installation, check for `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1\bin` and `CUDA_PATH` and `CUDA_PATH_V10_1` environment variables in the system environment variable configuration. Normally speaking, CUDA installed in lite mode will automatically add the corresponding system environment variables, no additional configuration is required.
- Add the `C:\Program Files\NVIDIA Corporation\NVSMI` folder to the system environment variable `Path`.
- Open `cmd` and enter the commands `nvcc -V` and `nvidia-smi` to check. If both respond, the CUDA installation was successful.



### 5. Download and Install cuDNN (If you want to use GPU to run Darknet)

- Make sure that CUDA is properly installed on the computer.

- Go to [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive) and download the corresponding cuDNN library according to CUDA version number, the corresponding library for GT 1030 is named: `cudnn-10.1-windows10-x64-v8.0.5.39.zip`. (**Not necessary if you have already downloaded**, downloading cuDNN requires you to register for an Nvidia developer account)

- Unzip the cuDNN archive, and you can see that the `cuda` directory in the resulting folder contains the `bin`, `include`, and `lib` directories.
- Find the installed CUDA directory (in lite mode, the installation directory is usually: `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1\)`, open it, and find the `bin`, `include`, and `lib` directories of CUDA
  - Copy **all .dll files** from the `{cuDNN_Extracted}\cuda\bin` folder after cuDNN has been extracted to the corresponding `bin` directory of CUDA.
  - Copy **all .h files** from the `{cuDNN_Extracted}\cuda\include` folder after cuDNN is extracted to the corresponding `include` directory of CUDA.
  - Copy **all .lib files** from the `{cuDNN_Extracted}\cuda\lib\x64` folder after cuDNN has been extracted to the corresponding `lib\x64` directory of CUDA.
- cuDNN Configuration Done.



### 6. Install OpenCV 4.5.5

This is a **MUST** Step if you want to preview the detection result directly. 

OpenCV Official Download Link: [Link](https://opencv.org/releases/)

- Go to OpenCV Official Release Page and Download OpenCV 4.5.5, Run the .exe file and let it extract itself to `D:\opencv\` .
- Add a variable named `OpenCV_DIR` to the system environment variable, with Value set to `D:\opencv\build (depending on the installation path)`
- Mind that what we configure here is the 64-bit CPU version of OpenCV. If you want to use 32-bit CPU version of OpenCV or OpenCV with CUDA, please refer to [Compile OpenCV with CMake](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake/)

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled%202.png" alt="Untitled" width="800px" /></div>

- Add `D:\opencv\build\x64\vc15\bin` to the system environment variable `Path`.



### 7. Compile Darknet with CMake

- Download CMake at [CMake Release](https://cmake.org/download/) and Choose the latest version to download and install. 
- Run CMake GUI, and compile darknet as the following picture instructed.

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled%203.png" alt="Untitled" width="700px" /></div>

- After Configuration, you can click "Generate" button to generate VS2019 Solution. Click "Open Project" button when generating done.
- Choose "Release x64" Platform, and Rebuild the solution, after rebuild, you can see `darknet.exe` in `darknet\Release`, copy it out to `darknet\build\darknet\x64` .
- Copy  `opencv_world455.dll` and  `opencv_ffmpeg455_64.dll`  (they are under opencv folder:  `…\opencv\build\x64\vc15\bin` ) to `darknet\build\darknet\x64`
- if you want to use GPU to speed up darknet, copy `cudnn64_7.dll` under cuDNN Extract folder to `darknet\build\darknet\x64`



### 8. Test if the darknet works

The above has completed the configuration of all environments and you can use yolov4.weights to implement target detection as follows.

First, download the `yolov4.weights` file from GitHub and copy this file to `darknet\build\darknet\x64`.

In the `D:\darknet\build\darknet\x64\` directory, use `cmd` to run: 

```bash
darknet.exe detector test cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote1.jpg
```

object detection of an image ( *remote1.jpg* ).



Use `cmd` to run: 

```bash
darknet.exe detector demo cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote_video.mp4
```

object detection of a video ( *remote.video.mp4* )



#### Powershell Command Line (Non-GPU Version)

##### YOLO v3 Tiny

Video Detection via YOLO v3 Tiny (Avg FPS around 4) with low recognition rate

```bash
.\darknet.exe detector demo cfg\coco.data cfg\yolov3-tiny.cfg yolov3-tiny.weights data\remote\remote.mp4
```

Image Detection via YOLO v3 Tiny (Relatively fast speed but very low recognition rate)

```bash
.\darknet.exe detector test cfg\coco.data cfg\yolov3-tiny.cfg yolov3-tiny.weights data\remote\remote1.jpg
```

Real-Time Detection using local USB Camera based on YOLOv3 Tiny

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



## In General

In fact, there is no need to configure the Darknet environment at all if you use the Python or C++ versions of the software to implement the detection (See [my GitHub projects](https://github.com/marc0cheung/YOLOv4_Detection_dnnOpenCV)). The Python and C++&Qt program is "out of the box", but the Pure C++ detector program needs to be configured with the OpenCV runtime environment in VS2019 to run.

The training environment is required to use Darknet. Therefore, the main reason for writing the Darknet configuration here is to allow you to train the deep learning network on your own computer after configuring the graphics card later. First you configure Darknet, then you train the network, get the weights, and then you can pass it into a C++ or Python real-time detection program for real-time detection.
