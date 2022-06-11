---
title: 'Train YOLOv4 weights using Google Colab'
date: 2022-06-10
permalink: /doc/2022/06/train-yolov4-colab/
tags:
  - YOLOv4
  - YOLOv4 Weights
  - Tutorials
  - Google Colab
---

<aside>
💡 Use this documentation to describe the steps engineers should follow to deploy.
</aside>


**Select Language**：English / [正體中文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-colab-zhHK/) / [简体中文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-colab-zhCN/)



> Colaboratory is a Google research project designed to help disseminate machine learning training and research results. It is a Jupyter notebook environment that requires no setup to use and runs entirely in the cloud.
>
> Colaboratory notebooks are stored on Google Cloud drives and can be shared, just as you would with Google Docs or Forms. colaboratory is free to use.
>
> With Colaboratory, it is easy to develop deep learning applications using frameworks such as Keras, TensorFlow, PyTorch, OpenCV and more. 



As we all know, GPUs are an important hardware need for deep learning development, and Google Colab amounts to giving us a powerful, easy to use and most importantly **free** GPU resource!

This tutorial will focus on the use and operation at the Google Colab level, see [Training your own YOLOv4 weights](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights/) for more information on preparing files for YOLOv4 training. 



## Document Transfer with Google Colab

Google Colab's file storage mechanism is similar to RAM - it is emptied after a power failure. When the user's runtime changes, or if the user closes the browser, then all the files uploaded by the user are lost. At the same time, Colab's file management user interface is very interactive and is only suitable for transferring a few small files. Therefore, it is important to have an efficient and stable way of transferring files for training deep learning networks with large and complex files.

After poking around, two methods have turned out to be more useful.

- Using GitHub to interact with Google Colab for files (for small files)
- Using Google Drive to interact with Google Colab (for most cases)

Here's how to do it. 



### Use GitHub to Transfer Files

- After logging into your GitHub account, Fork [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) to your GitHub account.

- Upload the training image material, `.cfg` file, `.data` file, `.names` file, `.txt` file, etc. to the Repo that you have Forked from your GitHub account.

- Open Google Colab, switch the runtime to the GPU version, and check that you have the GPU runtime with the following command.

```python
!nvidia-smi
!/usr/local/cuda/bin/nvcc --version
```

- In . /content/, the default directory, run the following command to put the prepared Repo git into Colab's storage.

```python
!git clone https://github.com/{username}/darknet.git
```

- Use `!ls` to check whether there are 3 folders under the `./content` directory

```python
darknet-master	drive  sample_data
```

- This completes the file transfer to Google Colab using GitHub.



As the Google Colab server is set up in the US, it is very fast in executing the `git clone` command, but it does have some drawbacks.

- **GitHub's file uploads are not fast** and can be unstable when there are a lot of files.
- **GitHub Webpage can only upload 100 files at a time**. When training a network, hundreds or thousands of images can be uploaded to GitHub in batches, which is a very tedious process.
- **GitHub cannot upload blank text files**. During training, a negative sample (i.e. an image without any targets in it) is tagged to give you a blank document, but GitHub does not support uploading empty documents, so it is not possible to pass a negative sample into the network for training.



### Use Google Drive to Transfer Files

Google Drive and Google Colab are both part of the Google family of products, and the two products support file interaction. So it is a very good, and even officially recommended, way to use Google Drive. The steps are as follows.

- First configure the darknet folder. If you don't know how to do this, see the end of this tutorial [Guide to storing pre-training content in the darknet folder].

- Upload the darknet folder to Google Drive.

- Open Google Colab, create a new project and switch to the runtime with the GPU.

- Execute the following command to mount Google Drive: 

```python
from google.colab import drive
drive.amount(/drive/MyDrive)
```

- Execute the `cp -r` command to copy the darknet folder from Google Drive to . /content/ directory.

```python
!cp -r ./drive/MyDrive/darknet/ ./content/
```

- At this point, the transfer of files between Google Drive and Google Colab has been successfully carried out.



Some users upload the configured darknet folder directly to Google Drive, and then execute commands such as `!make` and train the neural network in the darknet folder in the Drive directly after Colab is mounted on Google Drive. I think this is an unstable operation because Google Drive may break the link during the training process, and if it does, the training will be terminated directly, causing problems.

Therefore, I would prefer to run a cp command after mounting the Drive and copy the darknet folder to Colab's storage, so that the training will be more stable.

Of course, in some scenarios where the training takes a long time, some users train directly under the Google Drive path with the intention of using it as a backup. In this case, since Google Colab itself automatically disconnects users who stay connected for a long time, it is a good option to stay connected in this way. The only thing you need to do is to make sure that the external network connection is working and to avoid disconnecting Google Drive from Colab.



## Start Training with Google Colab

Once the files have been transferred, you can start training. Since Colab has already configured the CUDA, cuDNN and OpenCV environments, you only need to adjust the Makefile settings, compile darknet and then start training directly.

- Use the command `!cd darknet` to enter the darknet folder
- Run the following code to modify the Makefile to call CUDA, cuDNN and OpenCV for training.

```python
!sed -i 's/OPENCV=0/OPENCV=1' Makefile
!sed -i 's/GPU=0/GPU=1' Makefile
!sed -i 's/CUDNN=0/CUDNN=1' Makefile
```

- Execute the `!make` command to compile darknet

- After successful compilation, training can be performed using the training command as follows.

```python
./darknet detector train cfg/xxx.data cfg/xxx.cfg yolov4-tiny.conv.29 -dont_show

# As it is trained on the server, use the -dont_show command to avoid errors.
```





## Guide to storing pre-training content in the darknet folder

Before training, all the files need to be prepared and uploaded to Colab together for training. Try to start training with a single transfer to avoid hidden errors caused by repeated file transfers.

1. Download the darknet source code from [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet/) and extract it to a folder called `darknet-master`. Rename it to `darknet`. 
2. Since you are training on the server, you do not need to put the files under `darknet\build\darknet\x64` like Windows, but directly under `. /darknet/`
3. Put the `test` and `train` folders with the train and test images into `. /darknet/data/`.
4. Place the `test.txt` and `train.txt` files with the training clip images **paths** under `. /darknet/data/`
5. Place the `remote.names` file also under `. /darknet/data/`
6. Put `remote.cfg` and `remote.data` into `. /darknet/cfg/`
7. Place the COCO convolution weights `yolov4-tiny.conv.29` under `. /darknet/`



**File storage path preview as follows**.

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

