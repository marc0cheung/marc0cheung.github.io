---
title: '將 OpenCV 部署到 VS2019 開發環境中 (正體中文)'
date: 2022-06-07
permalink: /doc/2022/06/deploy-opencv-vs2019-zhHK/
tags:
  - OpenCV
  - VS2019
  - Tutorials
  - Deploy Environment
  - Traditional Chinese
---

## 步驟 0: 確保本教程適合你

**選擇語言:** [英文](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019/) / [簡體中文](https://marc0cheung.github.io/doc/2022/06/deploy-opencv-vs2019-zhCN/) / 正體中文



這篇教程是寫給那些想在自己的電腦上部署 64位 基於 CPU 的 OpenCV ，並用該框架用 Visual Studio 2019 進行應用程序開發的用戶們。

如你需要 32 位的 OpenCV ，或者需要 OpenCV 調用 CUDA 進行加速運算，那麽，需要使用 CMake 對 OpenCV 的源代碼進行重新編譯。



## 步驟 1: 下載-安裝 VS2019

- 前往 [Microsoft Visual Studio](https://visualstudio.microsoft.com) 官方網站下載并正確安裝 VS 2019。



## 步驟 2：下載并安裝 OpenCV 

- 前往 [OpenCV Release](https://opencv.org/releases/) 下載 OpenCV 4.5.5 或需要的版本。OpenCV 3 與 OpenCV 4 的配置方法只有些許不同。
- 運行 OpenCV 安裝包，將 OpenCV 解壓到 `D:\opencv\` 或者其它需要的路徑地址。本文使用 `D:\opencv455\opencv\` 作為示範，下同。
- 解壓后，打開系統環境變量，將 `D:\oepncv455\opencv\build\x64\vc15\bin` 添加到系統變量的 `Path` 中。



## 步驟 3：將 OpenCV 部署到 VS 2019

- 打開 VS 2019 ，創建一個新的帶有輸出 Hello World 程序的控制台程序。
- 在 `解決方案資源管理器` 中，在解決方案名稱上單擊右鍵，選擇 `屬性` ，確保自己的項目設定在所需要的平台上，例如本文以 `Release x64` 作為示範。
- 在 `C++` - `常規` - `附加包含目錄` 中，增加如下路徑：`D:\opencv455\opencv\build\include`  以及
  `D:\opencv455\opencv\build\include\opencv2` 
  - 注意，如果是 OpenCV 3，則還需要添加一個：
    `D:\opencv455\opencv\build\include\opencv`
- 在 `鏈接器` - `常規` - `附加庫目錄` 中，增加如下路徑：
  `D:\opencv\build\x64_dnn\vc16\lib`
- 在 `鏈接器` - `輸入` - `附加依賴項` 中，增加如下內容：
  `opencv_world455.lib` （若解決方案處於 Release 模式）
  如果解決方案處於 Debug 模式，則使用 `opencv_world455d.lib` 。



此時，應當可以在這個項目中使用 OpenCV 進行開發，可以利用下面的代碼，顯示一張圖片（圖片需要執行程序前自行存入），檢測 OpenCV 是否能正常工作。

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