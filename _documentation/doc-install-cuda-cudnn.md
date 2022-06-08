---
title: 'Install CUDA & cuDNN on Windows'
date: 2022-06-08
permalink: /doc/2022/06/install-cuda-cudnn-windows/
tags:
  - CUDA
  - cuDNN
  - Tutorials
  - Windows
---





**Select Language**: English

### 1. Download & Install CUDA (If you want to use GPU to run Darknet)

- Right Click on Windows desktop and select **NVIDIA Control Panel** 
- Find `NVCUDA.DLL` in the menu bar under `Help` > `System Information` > `Components` to see the supported CUDA version of your GPU, which is 10.1 for GT 1030.
- Download CUDA from [CUDA Archive](https://developer.nvidia.com/cuda-toolkit-archive) , be careful **NOT** to download a version higher than the version number you find from `NVCUDA.DLL`.

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-install-cuda-cudnn.assets/cuda_download.png" alt="cuda_download" width="600px" /></div>

- Run the local installation that you just download from CUDA Toolkit Website
- During installation, select the **"Lite"** installation mode. Installing CUDA will automatically install the appropriate drivers for the graphics card, so it is normal for the screen to flicker during installation.
- After installation, check for `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1\bin` and `CUDA_PATH` and `CUDA_PATH_V10_1` environment variables in the system environment variable configuration. Normally speaking, CUDA installed in lite mode will automatically add the corresponding system environment variables, no additional configuration is required.
- Add the `C:\Program Files\NVIDIA Corporation\NVSMI` folder to the system environment variable `Path`.
- Open `cmd` and enter the commands `nvcc -V` and `nvidia-smi` to check. If both respond, the CUDA installation was successful.



### 2. Download and Install cuDNN (If you want to use GPU to run Darknet)

- Make sure that CUDA is properly installed on the computer.

- Go to [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive) and download the corresponding cuDNN library according to CUDA version number, the corresponding library for GT 1030 is named: `cudnn-10.1-windows10-x64-v8.0.5.39.zip`. (**Not necessary if you have already downloaded**, downloading cuDNN requires you to register for an Nvidia developer account)

- Unzip the cuDNN archive, and you can see that the `cuda` directory in the resulting folder contains the `bin`, `include`, and `lib` directories.
- Find the installed CUDA directory (in lite mode, the installation directory is usually: `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.1\)`, open it, and find the `bin`, `include`, and `lib` directories of CUDA
  - Copy **all .dll files** from the `{cuDNN_Extracted}\cuda\bin` folder after cuDNN has been extracted to the corresponding `bin` directory of CUDA.
  - Copy **all .h files** from the `{cuDNN_Extracted}\cuda\include` folder after cuDNN is extracted to the corresponding `include` directory of CUDA.
  - Copy **all .lib files** from the `{cuDNN_Extracted}\cuda\lib\x64` folder after cuDNN has been extracted to the corresponding `lib\x64` directory of CUDA.
- cuDNN Configuration Done.
