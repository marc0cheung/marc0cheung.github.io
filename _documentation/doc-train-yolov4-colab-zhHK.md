---
title: '用 Google Colab 訓練自己的 YOLOv4 權重'
date: 2022-06-10
permalink: /doc/2022/06/train-yolov4-colab-zhHK/
tags:
  - YOLOv4
  - YOLOv4 Weights
  - Tutorials
  - Google Colab
  - Traditional Chinese
---

<aside>
💡 Use this documentation to describe the steps engineers should follow to deploy.
</aside>


**選擇語言**：[English](https://marc0cheung.github.io/doc/2022/06/train-yolov4-colab/) / 正體中文 / [简体中文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-colab-zhHK/)



> Colaboratory 是一個 Google 研究項目，旨在幫助傳播機器學習培訓和研究成果。它是一個 Jupyter 筆記本環境，不需要進行任何設置就可以使用，併且完全在雲端運行。
>
> Colaboratory 筆記本存儲在 Google 雲端硬盤中，併且可以共享，就如同您使用 Google 文檔或錶格一樣。Colaboratory 可免費使用。
>
> 利用Colaboratory，可以方便的使用Keras, TensorFlow, PyTorch, OpenCV等框架進行深度學習應用的開發。

眾所周知，GPU 是深度學習開發的重要硬件需要，而 Google Colab 相當於給我們提供了一個功能強大，使用方便，最重要是**免費**的GPU資源！



本教程將主要關註在 Google Colab 層面的使用和操作，關於 YOLOv4 訓練前的文件准備工作，請參閱 [訓練自己的 YOLOv4 權重](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights-zhCN/) 。



## 與 Google Colab 進行文件交互

Google Colab 的文件存儲機制類似於 RAM —— 斷電後即清空。當用戶的運行時（Run Time）發生改變，或者用戶將瀏覽器關閉了，那麽用戶上傳的文件將全部丟失。同時，由於 Colab 的文件管理用戶界面交互體驗奇差無比，僅僅適用於少數小體積文件的傳輸。所以，針對訓練深度學習網絡文件多且復雜的情況，必須要有一種高效、穩定的文件傳輸方式。

經過摸索，發現有兩種方法是比較好用的：

- 利用 GitHub 與 Google Colab 進行文件交互（適用於文件較少的情況）
- 利用 Google Drive 與 Google Colab 進行文件交互（適用於大部分情況）

具體的操作方法如下。



### 利用 GitHub 進行文件傳輸

- 登錄自己的 GitHub 賬號後，將 [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) Fork 到自己的 GitHub 賬戶下。

- 將訓練圖片素材、`.cfg` 文件、`.data` 文件、`.names` 文件、`.txt` 文件等上傳到自己 GitHub 賬戶中 Fork 來的 Repo 中。

- 打開 Google Colab，切換運行時到 GPU 版本，利用如下命令檢查是否獲得了 GPU 運行時：

  ```python
  !nvidia-smi
  !/usr/local/cuda/bin/nvcc --version
  ```

- 在 ./content/ 即默認目錄下，執行如下命令將准備好的 Repo git 到 Colab 的 存儲空間中：

  ```python
  !git clone https://github.com/{username}/darknet.git
  ```

- 利用 `!ls` 指令，檢查 ./content 目錄下是否有如下三個文件夾：

  ```bash
  darknet-master	drive  sample_data
  ```

- 至此，完成了利用 GitHub 對 Colab 的文件傳輸。

由於 Google Colab 服務器架設在美國，它執行 git clone 命令的速度是非常快的，但它也有一定的缺點：

- **GitHub 的文件上傳速度併不快**，遇到文件較多的情況下有不穩定的情況出現。
- **GitHub Webpage 一次只能上傳 100 個文件**。在訓練網絡時，數百上仟張圖片如果分批上傳到 GitHub，操作過程非常繁瑣。
- **GitHub 無法上傳空白文本文件**。在訓練過程中，對負樣本（即圖片中沒有任何目標的情況）進行打標，會得到一個空白的文檔，但 GitHub 併不支持空文檔的上傳，所以進而無法將負樣本傳入網絡中訓練。



### 利用 Google Drive 進行文件傳輸

Google Drive 與 Google Colab 同屬 Google 繫列的產品，兩個產品之間是支持文件交互的。所以也是一種非常好的，乃至 Google 官方推薦使用的辦法。具體操作步驟如下：

- 首先配置好 darknet 文件夾。若不知如何配置，可參見本教程末尾【darknet 文件夾訓練前內容存放指南】

- 將 darknet 文件夾上傳到 Google Drive 中。

- 打開 Google Colab，新建項目，併切換到帶 GPU 的運行時。

- 執行以下命令，掛載 Google Drive: 

  ```python
  from google.colab import drive
  drive.amount(/drive/MyDrive)
  ```

- 執行 `cp -r` 指令，將 Google Drive 中的 darknet 文件夾拷貝到 ./content/ 目錄中：

  ```python
  !cp -r ./drive/MyDrive/darknet/ ./content/
  ```

  

- 用 `!ls` 指令確認 ./content/ 目錄下是否有如下三個文件夾：

  ```python
  darknet  drive  simple
  ```

- 至此，就已經成功通過 Google Drive 與 Google Colab 實現文件傳輸。



有些用戶會直接將配置好的 darknet 文件夾上傳到 Google Drive 中，在 Colab 掛載了 Google Drive 後，直接在 Drive 中的 darknet 文件夾執行 `!make` 、訓練神經網絡 等命令。我認為這是一種不穩定的操作，因為 Google Drive 在訓練過程中有斷開鏈接的可能，一旦斷開，訓練將直接終止，帶來麻煩。

所以，我更加推薦在掛載 Drive 後，多執行一條 cp 指令，將 darknet 文件夾復制到 Colab 的存儲空間中，這樣訓練將更加穩定。

當然，在有些訓練時長比較久的場景下，有部分用戶直接在 Google Drive 路徑下進行訓練，是有用作備份的意圖的。這種情況下，由於 Google Colab 本身會對長時間保持連接的用戶自動斷開，用這種方法保持連接也是一個好選擇。唯需要註意保證外網連接正常，盡量避免 Google Drive 與 Colab 斷開連接。



## 用 Google Colab 開始訓練

文件傳輸完畢後，就可以開始訓練了，由於 Colab 已經配置好了 CUDA、cuDNN 和 OpenCV 環境，所以只需要調整 Makefile 設置，編譯 darknet 後，直接開始訓練就可以了。

- 使用 `!cd darknet` 命令，進入 darknet 文件夾

- 運行如下代碼，修改 Makefile ，使其調用 CUDA、cuDNN 以及 OpenCV 進行訓練：

  ```python
  !sed -i 's/OPENCV=0/OPENCV=1' Makefile
  !sed -i 's/GPU=0/GPU=1' Makefile
  !sed -i 's/CUDNN=0/CUDNN=1' Makefile
  ```

- 執行 `!make` 指令對 darknet 進行編譯

- 編譯成功後，使用訓練指令進行訓練即可，如下：

  ```python
  ./darknet detector train cfg/xxx.data cfg/xxx.cfg yolov4-tiny.conv.29 -dont_show
  
  # 由於是在服務器上訓練，所以使用 -dont_show 指令避免出錯。
  ```

  



## darknet 文件夾訓練前內容存放指南

在訓練之前，需要將所有文件都准備妥當，一起上傳到 Colab 中進行訓練。盡量能做到一次傳輸即可開始訓練，避免文件重復傳輸導致隱藏的錯誤發生。

1. 從  [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) 下載 darknet 源碼後，解壓出來得到一個名為 `darknet-master` 的文件夾。將其重命名為 `darknet` 。
2. 由於是在服務器上訓練，所以不需要像 Windows 一樣將文件放到 `darknet\build\darknet\x64` 下，而是直接放到 `./darknet/` 下即可
3. 將存有訓練素材圖片的 `test` 和 `train` 文件夾放入 `./darknet/data/` 下。
4. 將存有訓練素材圖片**路徑**的 `test.txt` 與 `train.txt` 文件也放入 `./darknet/data/` 下。
5. 將 `remote.names` 文件也放入 `./darknet/data/` 下。
6. 將 `remote.cfg` 與 `remote.data` 放入 `./darknet/cfg/` 下。
7. 將 COCO 卷積權重 `yolov4-tiny.conv.29` 放入 `./darknet/` 下。



**文件存儲路徑預覽如下**：

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

