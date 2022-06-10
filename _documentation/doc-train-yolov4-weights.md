---
title: 'Train .weights File for YOLOv4 on Windows'
date: 2022-06-08
permalink: /doc/2022/06/train-yolov4-weights/
tags:
  - YOLOv4
  - YOLOv4 Weights
  - Tutorials
  - Windows
---



**Select Language**: English / [Traditional Chinese](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights-zhHK/) / [Simplified Chinese](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights-zhCN/)

First, make sure that the darknet on your PC can function properly via `cmd` or `Windows Powershell` . Otherwise you need to refer to this [Deploy YOLOv4](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows/) tutorial to properly configure darknet first.



### 1. Label the Images Before Training

- Get some images of the target object, for example, 500 images of a TV remote.
- A random classification of all images into a training set and a test set (with a self-determined ratio between the two), e.g. `train_remote_imgs` and `test_remote_imgs`.
- Use the labelImg annotation tool to annotate these images with the target to be identified. The annotation data is in json format, i.e. a label file for each image. (**If this is for YOLOv4, be sure to pre-select the YOLO format so you don't need to convert it**; if you need to convert it, see `XML_to_YOLO.py` included at the end of this tutorial)
- Using Python code ( `Split_Data.py`, placed at the end of this tutorial) to randomly split the images into a training set and a validation set, or randomly selected by hand. 
  Note, however, that the images in the training and test sets cannot be duplicated. That is to say, you cannot have the same image in both the training set and the test set.
- Use the code (`XML_to_YOLO.py` included at the end of this tutorial) to convert the XML markup file to a YOLO-supported txt format file (you don't need to do this if you have selected the YOLO format in LabelImg beforehand, **so make sure you select the format beforehand! **)

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-train-yolov4-weights.assets/ChooseCorrectFormat.png" alt="ChooseCorrectFormat" style="zoom:60%;" /></div>



### 2. Prepare the .txt Files for Training

The following code (`label2train.py`) is used to generate `train.txt` and `test.txt` files. The contents of `train.txt` are the paths to all the training data for training, while the contents of `test.txt` are the paths to all the test set data for testing.

Before generating the `.txt` file, `label2train.py` needs to be placed in the appropriate location for execution. The corresponding training material file structure directory is shown below.

```
YOLOTraining
|_____ test
        |_____ 1.png
        |_____ 1.txt
        |_____ ...
        |_____ n.png
        |_____ n.txt
|_____ train
        |_____ 3.png
        |_____ 3.txt
        |_____ ...
        |_____ x.png
        |_____ x.txt
|_____ label2train.py
|_____ test.txt  (Will be Generated after running label2train.py)
|_____ train.txt (Will be Generated after running label2train.py)
```



`label2train.py` 

```python
# label2train.py
import os
from os import listdir, getcwd
from os.path import join

print("Please select system type: ")
system_type = str(input("(u)nix or (w)indows?: "))
 
wd = str(input('Folder Name: '))
 
g = os.walk(wd)
 
list_file = open('%s.txt' %(wd), 'w')
 
for path,d,filelist in g:  
    for filename in filelist:
        if filename.endswith('png'):
            if system_type == "u":
                # for Unix
                list_file.write('data/%s/%s\n'%(wd, filename))
            elif system_type == "w":
                # for Windows
                list_file.write('data\\%s\%s\n'%(wd, filename))
list_file.close()
```



⚠️ Note that the path symbol for training in Windows should be `\`, while we use `/` in Unix-like system. So be aware of the generation result to avoid errors. 
The program above has a special system type switch. `u` for the unix class and `w` for the Windows style.



### 3. Start Training (Use YOLO-Fastest network as an Example)

- Use the Powershell Command Line below to train .weights file of your own using darknet: 

```bash
./darknet.exe detector train ./cfg/xxx.data ./cfg/xxx.cfg yolov4-tiny.conv.29 -cpu

# or change to -gpu to use GPU for Training
```



The  `.conv.xxx` file, is something called `Convolution weights` . This is the file that must be used in the process of training your own .weights file. Some `Convolution weights` can be downloaded from the Internet, but some are not available on the Internet. In this case you will need to generate them yourself via a command. Of course, the prerequisite for generating convolutional weights is to have the corresponding coco pre-training weights for the network in the corresponding directory of the folder in advance, such as the following command: `Yolo-Fastest\COCO\yolo-fastest-1.1-xl.weights`

#### Method for generating pre-training weights

- Enter the folder of the compiled darknet，for example `D:\darknet\build\darknet\x64` ，Use the Powershell Command below under this path: 

```bash
.\darknet.exe partial Yolo-Fastest\COCO\yolo-fastest-1.1-xl.cfg Yolo-Fastest\COCO\yolo-fastest-1.1-xl.weights yolo-fastest.conv.109 109
```

-  A COCO pre-training model file called `yolo-fastest.conv.109` can be obtained in the x64 folder.



#### Modify .cfg File

- Make a copy of `yolo-fastest-1.1-x1.cfg` and rename it to the name you want, e.g. `remote.cfg` 
  Open it with a text editor and make changes to this file. Modify it as follows.

  - **Step 1**: Change classes = `the number of target classes contained in your own training set`, e.g. if you need the network to recognise `TV remote` and `mouse`, then this value is `2`. Change all the `classes` values in the `.cfg` file that you can search for.

  - **Step 2**: Modify `filters = 3 * (class_num + 5)=18` in [convolutional] above [yolo], where `class_num` is the `number of target classes contained in your own training set`. Remember to change **all** the [convolutional] above [yolo].

  - **Step 3**: Change `max_batches` and `steps`, i.e. the number of training batches and training rounds.
    Generally speaking， `max_batches`  at its minimum is 6000. So, for 1 class, set this value to 6000. 
    While `steps = max_batches * 0.8, max_batches*0.9` So, for 1 class, set `steps` to  `steps=4800, 5400` 

    Note: The exact time to stop can be determined by observing `avg_loss` during the training process. When `avg_loss` is no longer decreasing, then training can be stopped. 

  - **Step 4**: The `width` and `height` under [net] allow you to set the resolution of the training image. The larger the setting, the longer it will take, so you can set the size according to the actual training image material. However, it must be set to a multiple of 32. The ratio is 1:1, usually set to 416 * 416.

  - **Step 5**: The `batch` and `subdivisions` under [net] do not need to be modified here as they are for training. However, if you get a `CUDA out of memory` message during training, you can increase the `subdivisions` here, but the larger the value, the longer the training will take, so be sure to make the trade-off before modifying it.

For `batch` and `subdivisions` , you can choose **16, 32, 64** for `batch` and **4, 8, 16** for `subdivisions`.



#### Modify .names File and .data File

- Find a random `.names` file in the darknet/cfg folder, make a copy of it and rename it to the name you want, e.g. `remote.names` 
  This file is used to store the names of the categories of items to be identified. One line per name. If there is only one class, the file will have only one line, as follows.

```bash
remote
```

- Create a new file called `remote.data` to hold some content about the training data, as follows.

```bash
classes = 1               # number of training classes
train = data\train.txt    # path to the training set images
valid = data\test.txt     # path to the validation set of images
names = data\remote.names # the names of the training categories
backup = backup\          # Path to save the training output model 

#If the relative path does not work, replace it with an absolute path
```

⚠️ If you use Ubuntu or other Unix-Like OS for training, you need to change  `\` to  `/` in order to match the OS Path Slash Style. 

The `.txt` file in the `.data` file records the location of the images used for training and testing.

- Open Powershell under `darknet\build\darknet\x64` and train with the following command (in PowerShell, you can leave the `/` slash alone, but in the `cmd` may have to be changed).

```bash
./darknet.exe detector train ./cfg/remote.data ./cfg/remote.cfg yolo-fastest.conv.109
```

Add the `-dont_show` parameter as appropriate. If you are using a server without an external display, you will need to add this parameter to avoid errors.

Add `-gpu` at the bottom of the command if you want to use GPU for training.



#### Where to put these files mentioned above?

Put `test` and `train` folder under `darknet\build\darknet\x64\data` 

Put `remote.names` , `train.txt` and `test.txt` under `darknet\build\darknet\x64\data`

Put `yolo-fastest.conv.109` under `darknet\build\darknet\x64` 

Put `remote.cfg` and `remote.data` under `darknet\build\darknet\x64\cfg`



**File Structure of the Training Files**：

```
darknet\build\darknet\x64
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
|_____ yolo-fastest.conv.109
```







### Split_Data.py

```python
# Split_Data.py

import os
import glob
import json
import shutil
import numpy as np
import xml.etree.ElementTree as ET

img_train = './images/train/'
img_val = './images/valid/'

label_train = './labels/train/'
label_val = './labels/valid/'

allimgs = glob.glob('original_dataset/train/' + "/*.jpg")
allimgs = np.sort(allimgs)
np.random.seed(100)
np.random.shuffle(allimgs)

train_ratio = 0.9
train_num = int(len(allimgs) * train_ratio)

# get train list and validate list
img_list_train = allimgs[:train_num]
img_list_val = allimgs[train_num:]

# create new folder
if os.path.exists(img_train):
    shutil.rmtree(img_train)
    os.mkdir(img_train)
else:
    os.mkdir(img_train)

if os.path.exists(img_val):
    shutil.rmtree(img_val)
    os.mkdir(img_val)
else:
    os.mkdir(img_val)

if os.path.exists(label_train):
    shutil.rmtree(label_train)
    os.mkdir(label_train)
else:
    os.mkdir(label_train)

if os.path.exists(label_val):
    shutil.rmtree(label_val)
    os.mkdir(label_val)
else:
    os.mkdir(label_val)

# move val data to certain path
for i in img_list_val:
    img_id = i.split('.')[0].split('/')[2]
    print(img_id)
    # jpg
    shutil.copy(i, img_val + img_id + '.jpg')
    # xml
    shutil.copy('original_dataset/label/' + 'new_' + img_id + '.xml', label_val + img_id + '.xml')

# move train data to certain path
for i in img_list_train:
    img_id = i.split('.')[0].split('/')[2]
    print(img_id)
    # jpg
    shutil.copy(i, img_train + img_id + '.jpg')
    # xml
    shutil.copy('original_dataset/label/' + 'new_' + img_id + '.xml', label_train + img_id + '.xml')
```



### XML_to_YOLO.py

```python
import xml.etree.ElementTree as ET
import os
from os import getcwd
from os.path import join
import glob

sets = ['train', 'test']
classes = ['remote']  

def convert(size, box):
    dw = 1. / size[0]
    dh = 1. / size[1]
    x = (box[0] + box[1]) / 2.0
    y = (box[2] + box[3]) / 2.0
    w = box[1] - box[0]
    h = box[3] - box[2]
    x = x * dw
    w = w * dw
    y = y * dh
    h = h * dh
    return (x, y, w, h)

def convert_annotation(data_dir, imageset, image_id):
    in_file = open(data_dir + '/%s_annotations/%s.xml' % (imageset, image_id))  # read xml
    out_file = open(data_dir + '/%s_labels/%s.txt' % (imageset, image_id), 'w')  # save as txt

    tree = ET.parse(in_file)
    root = tree.getroot()
    size = root.find('size')
    w = int(size.find('width').text)
    h = int(size.find('height').text)
    for obj in root.iter('object'):
        difficult = obj.find('difficult').text
        cls = obj.find('name').text
        if cls not in classes or int(difficult) == 1:
            continue
        cls_id = classes.index(cls)  # get index
        xmlbox = obj.find('bndbox')
        b = (float(xmlbox.find('xmin').text), float(xmlbox.find('xmax').text), float(xmlbox.find('ymin').text),
             float(xmlbox.find('ymax').text))
        bb = convert((w, h), b)
        out_file.write(str(cls_id) + " " + " ".join([str('%.6f' % a) for a in bb]) + '\n')

wd = getcwd()
print(wd)  # current path

data_dir = 'F:/Postgraduate_time/label_fish'

for image_set in sets:
    image_ids = []
    for x in glob.glob(data_dir + '/%s_annotations' % image_set + '/*.xml'):
        print(x)
        image_ids.append(os.path.basename(x)[:-4])
    print('\n%sAmount:' % image_set, len(image_ids))  # Confirm Amount
    i = 0
    for image_id in image_ids:
        i = i + 1
        convert_annotation(data_dir, image_set, image_id)
        print("%s Data:%s/%s File Done！" % (image_set, i, len(image_ids)))

print("Done!!!")
```

