---
title: '在 Windows 10 上部署 YOLOv4 檢測網路（基於 Darknet）'
date: 2022-06-08
permalink: /doc/2022/06/deploy-yolov4-windows-zhHK/
tags:
  - YOLOv4
  - Darknet
  - Tutorials
  - Windows
  - Traditional Chinese
---



**選擇語言**：[English](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows/) / 正體中文 / [简体中文](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows-zhCN/)



### 1. 由 GitHub 下載 YOLOv4 源代碼

从  [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) 下載最新的 YOLOv4 原始碼，解壓縮下載所得到的 .zip 文件，并將它放置在一個自定義位置。例如本文以 `D:\darknet\` 作為示範。



### 2. 安裝 LabelImg 打標軟件（如有需要）

確保電腦上已正確安裝好 Python 環境。可以在 `cmd` 中輸入 `python` 指令，來查看是否已經有存在的 Python 環境。

- 在 `cmd` 中，輸入指令：`pip install labelimg`
- 日後運行 LabelImg 的方法：在 `cmd` 中鍵入 `labelimg` 指令并運行即可。



### 3. 安裝 VS 2019

前往 [Microsoft Visual Studio](https://visualstudio.microsoft.com/) 下載、安裝 VS 2019。



### 4. 下載 CUDA （如需要令 Darknet 使用 GPU 進行演算）

請參考我的博客文章：[How To Install CUDA & cuDNN on Windows 10](https://marc0cheung.github.io/doc/2022/06/install-cuda-cudnn-windows/)



### 5. 下載 cuDNN （如需要令 Darknet 使用 GPU 進行演算）

請參考我的博客文章：[How To Install CUDA & cuDNN on Windows 10](https://marc0cheung.github.io/doc/2022/06/install-cuda-cudnn-windows/)



### 6. 安裝 OpenCV 4.5.5

OpenCV Official Download Link: [Link](https://opencv.org/releases/)

解壓後設置環境變量：在系統環境變量中添加一個名為 `OpenCV_DIR` 的變量（如下圖所示），Value 設置為 `D:\opencv\build （取決於安裝路徑）`

在系統環境變量的 `Path` 中，添加路徑 `D:\opencv455\opencv\build\x64\vc15\bin` ，這裏的路徑為 OpenCV 所解壓到的目錄。如果需要 32位 的 OpenCV，或者 未來在 VS2019 開發中需要用到 OpenCV with CUDA ，則需要自行通過 CMake 編譯。這一點在另一篇說明文檔中將會提到。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled%202.png" alt="Untitled" width="800px" /></div>



### 7. 使用 CMake 編譯 Darknet

前往 [CMake Download](https://cmake.org/download/) 下載最新版本的 CMake 并安裝。安裝完畢后，啟動 CMAKE GUI，按照下圖流程操作。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-deploy-yolov4-windows.assets/Untitled%203.png" alt="Untitled" width="700px" /></div>

完成 Configure 流程之後 CMake 會自動進行編譯，選擇 Open Project ，在 VS 中選擇 Release x64 平台，重新生成解決方案。編譯完畢後，可以在 `darknet\Release` 內看到編譯得到的 `darknet.exe` ，將該文件拷貝到 `darknet\build\darknet\x64` 中。

將 `…\opencv\build\x64\vc14\bin` 下的兩個 `opencv_world455.dll` 和  `opencv_ffmpeg455_64.dll`  文件復製到上述文件夾內。

（若有使用GPU）還需要將 cuDNN 中的 `cudnn64_7.dll` 復製到上述文件夾。



### 8. 測試 Darknet 是否工作

經過上述的配置，這時你应當能使用 `yolov4.weights` 文件進行目標檢測。可以參考如下步驟檢測 Darknet 是否工作。

首先，从 GitHub 上下載 `yolov4.weights` 文件，將這個文件放在 `darknet\build\darknet\x64` 中。

在 `D:\darknet\build\darknet\x64\` 文件夾中，使用 `cmd` 運行： 

```bash
darknet.exe detector test cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote1.jpg
```

對一個圖像進行目標檢測 ( *remote1.jpg* ).



Use `cmd` to run: 

```bash
darknet.exe detector demo cfg\coco.data cfg\yolov4.cfg yolov4.weights data\remote_video.mp4
```

對一個視頻進行目標檢測 ( *remote.video.mp4* )



#### Powershell 命令行指令 (无 GPU 版本)

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



### 總結

實際上，如果用 Python 或者 C++ 版本的軟件實現檢測，完全不需要配置 Darknet 環境（參考[我的項目](https://github.com/marc0cheung/YOLOv4_Detection_dnnOpenCV)）。直接將訓練好的網絡文件傳進 Python 或 C++ 程序中即可。Python 程序是「開箱即用」的，但 C++ 程序還需要在 VS2019 中配置好 OpenCV 運行環境方可運行。

訓練環境是必須要使用 Darknet 的。所以，在這裏寫 Darknet 的配置方法，主要是為了日後配置顯卡後，可以在自己的計算機上進行訓練深度學習網絡。先配置好 Darknet ，再訓練網絡，得到權重，就可以傳進 C++ 或 Python 的實時檢測程序中使用了。