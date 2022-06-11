---
title: '用 CMake 編譯 OpenCV with CUDA'
date: 2022-06-10
permalink: /doc/2022/06/compile-opencv-cuda-cmake-zhHK/
tags:
  - OpenCV
  - CUDA
  - CMake
  - Tutorials
  - Traditional Chinese
---

選擇語言：[英文](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake/) / 正體中文 / [简体中文](https://marc0cheung.github.io/doc/2022/06/compile-opencv-cuda-cmake-zhCN/)



### Step 1: 安裝 CUDA 与 cuDNN 環境

首先，跟從 [這篇指南](https://marc0cheung.github.io/doc/2022/06/install-cuda-cudnn-windows/) 正確安裝 CUDA 与 cuDNN 環境。



### Step 2: 編譯 OpenCV with CUDA

 - 提醒：OpenCV在官網上下載到的版本是 `64位CPU版` 。如果需要 `32位CPU版` 或 `64位GPU版` 則均需要自行編譯。註意，用 cMake 與 VS2019 編譯 `32位GPU版` 時，會出現報錯無法編譯的情況，原因未知。猜測可能是 CUDA 不再支持 32位 的應用程序了。
 - 前往 [cMake Download](https://cmake.org/download/) 下載 cMake 最新版本。
 - 前往 [OpenCV Releases](https://opencv.org/releases/) 下載 OpenCV 4.5.5 源碼包（Source）。或者直接下載 Windows 版本的 `.exe` 文件，程序自解壓後亦會有源碼保存在 `source` 文件夾中。
 - 前往 [OpenCV Contrib Release Tags](https://github.com/opencv/opencv_contrib/tags) 下載對應版本的 OpenCV Contrib 庫源代碼。因為 CUDA 加速等比較新的特性，OpenCV 將其放置在 opencv_contrib 庫中。將下載到的文件解壓，放置到 OpenCV 的 sources 文件夾下。
 - 在 OpenCV 的 build 文件夾下新建一個 `x64_cuda_build` 文件夾，打開 cMake ，選擇 source 和 build 對應的存放位置，點擊 Configure 按鈕，選擇 VS2019 和 x64 ，確認。
 - 在 cMake 界面中，按照下列描述，調整各選項：

```
啟用：WITH_CUDA
啟用：OPENCV_DNN_CUDA
啟用：OPENCV_ENABLE_NONFREE
啟用：CUDA_FAST_MATH
啟用：BUILD_opencv_world

禁用：ENABLE_FAST_MATH
禁用：BUILD_CUDA_STUBS
禁用：搜索 python 後的所有內容
禁用：搜索 java 後的所有內容
禁用：搜索 test 後的所有內容

OPENCV_EXTRA_MODULES_PATH 選擇 opencv_contrib 目錄下的 modules 文件夾
CUDA_ARCH_BIN：選擇自己顯卡的算力版本，例如 Pascal 架構顯卡為 6.1; 剩下的全部去掉。

```

- 按照上述內容配置完畢後，再次點擊 configure 按鈕，如果剛開始上述的某些選項沒有出現，請點第一次 configure 按鈕後再搜索。
- **對於身處中國的用戶**，cMake 在編譯過程中會自動從 GitHub 上下載一些內容，此時可能由於網絡原因無法下載。如果 cMake 在編譯過程出現查看 cMakeDownloadLog.txt 的提示，用戶應當按照 [這篇文章](https://blog.csdn.net/painice/article/details/123347824) 來解決問題。
- 當 cMake 沒有任何紅色錯誤出現後，查看 cMake 輸出信息中，是否在 CUDA 處有對應的版本號，cuDNN 處是否顯示 `CUDNN: YES (Ver X.X.X)` ，以及 CUDA_ARCH_BIN 處是否顯示顯卡的算力版本。
- 當 cMake 的一切都沒有問題，即可選擇 `Open Project` 按鈕，用 VS2019 打開項目，選擇 `生成` > `批生成` > 勾選 INSTALL 的 Debug 和 Release 版本，選擇生成。
- 經過漫長的等待，我們即可在 `x64_cuda_build` 文件夾下看到 `install` 文件夾，內有 `x64` 文件夾，即我們需要用到的 `.dll` 文件。
