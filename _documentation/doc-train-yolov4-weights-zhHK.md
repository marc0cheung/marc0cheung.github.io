---
title: '在 Windows 上訓練自己的 YOLOv4 權重 (.weights) 文件'
date: 2022-06-08
permalink: /doc/2022/06/train-yolov4-weights-zhHK/
tags:
  - YOLOv4
  - YOLOv4 Weights
  - Tutorials
  - Windows
---





**選擇語言**: [English](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights/) / 正體中文 / [简体中文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights-zhCN/)

首先要確定自己電腦上的 darknet 可以正常利用命令行工作，如果不可以，則參考 [部署 YOLOv4](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows-zhHK/) 部分的內容。



### 1. 訓練前數據標記

- 獲取某個目標的若幹照片，例如500張包含遙控器的照片；
- 對所有的圖片進行隨機分類，分為訓練集和測試集（二者的比例自行確定），例如  `train_remote_imgs` 以及 `test_remote_imgs` ；
- 使用 labelImg 標註工具對這些圖片中的要識別的目標進行標記，標記的數據格式為 json 格式，即每張圖片的 label 文件。（**如果是給 YOLOv4 ，則一定要預先選擇YOLO格式，就不用轉換了**；如果需要轉換，可參見本文末尾收錄的 `XML_to_YOLO.py` ）
- 利用 Python 代碼（放置於末尾的 `Split_Data.py` ）將圖片隨機分為訓練集和驗證集，手工隨機抽取也可以。但註意，訓練集和測試集的圖片是不能重復的。即不能同一張圖片，既出現在訓練集，又出現在測試集。
- 利用代碼（本文末尾收錄的 `XML_to_YOLO.py` ）將 XML 標註文件轉換為 YOLO 支持的 txt 格式文件（如果提前在 LabelImg 中選擇了 YOLO 格式則不需要做這個處理了，**所以一定要提前選格式！！**）

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-train-yolov4-weights.assets/ChooseCorrectFormat.png" alt="ChooseCorrectFormat" style="zoom:60%;" /></div>



### 2. 準備訓練用的 .txt 文件

利用如下代碼 ( `label2train.py` 文件) 生成 `train.txt` 和 `test.txt` 兩個文件， `train.txt` 的內容是全部訓練數據的路徑，作為訓練用；而 `test.txt` 的內容是全部測試集數據的路徑，用作測試。

在生成 `.txt` 文件之前，需要將 `label2train.py` 放到恰當的位置執行。對應的訓練素材文件結構目錄如下圖所示。

```
YOLOTraining (用於存放訓練素材的文件夾)
|_____ test
|       |_____ 1.png
|       |_____ 1.txt
|       |_____ ...
|       |_____ n.png
|       |_____ n.txt
|_____ train
|       |_____ 3.png
|       |_____ 3.txt
|       |_____ ...
|       |_____ x.png
|       |_____ x.txt
|_____ label2train.py
|_____ test.txt (調用 label2train.py 後生成)
|_____ train.txt (調用 label2train.py 後生成)
```



`label2train.py` 代碼如下：

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



⚠️ 註意：在Windows中訓練的路徑符號應該是 `\` ，要註意生成的情況，避免出現錯誤。所以上述程序中專門做了系統類型切換。u 代表 unix 類，w 代表 Windows 風格。



### 3. 開始訓練（以訓練 YOLO-Fastest 網絡為例）

- darknet 訓練權重的 PowerShell 命令如下：

```bash
./darknet.exe detector train ./cfg/xxx.data ./cfg/xxx.cfg yolov4-tiny.conv.29 -cpu

# 或者 -gpu 利用顯示卡訓練模型
```



最後的 `.conv.xxx` 格式文件，稱為 `預訓練權重` ，是訓練自己的網絡所必須的文件。有些 `預訓練權重` 可以在網絡上下載到，但有些網絡上無法找到。此時需要自己通過指令生成。

#### 生成預訓練權重的方法

- 進入編譯後得到的 darknet 的文件夾，例如 `D:\darknet\build\darknet\x64` ，在該路徑的 PowerShell 下鍵入如下指令：

```bash
.\darknet.exe partial Yolo-Fastest\COCO\yolo-fastest-1.1-xl.cfg Yolo-Fastest\COCO\yolo-fastest-1.1-xl.weights yolo-fastest.conv.109 109
```

-  可以在 x64 文件夾下，得到一個名為 `yolo-fastest.conv.109` 的 COCO 預訓練模型 文件。



#### 修改 .cfg 文件

- 把 `yolo-fastest-1.1-x1.cfg` 復製一份，改名為自己需要的名字，例如 `remote.cfg` 
  用文本編輯器打開，對這個文件進行修改。修改步驟如下所示：

  - Step 1: 修改 classes = `自己訓練集中包含的目標類別數` ，例如，需要網絡去識別 `遙控器` 和 `鼠標` ，那麽這個值就是 `2` 。把 `.cfg` 文件中能搜索到的所有 `classes` 值都改了。

  - Step 2: 修改 [yolo] 上一層 [convolutional] 裏的 `filters = 3 * (class_num + 5)=18` ，這裏的 `class_num` 是  `自己訓練集中包含的目標類別數` 。也是把所有 [yolo] 上方的 [convolutional] 都改掉。

  - Step 3: 更改 `max_batches` 和 `steps` ，也就是訓練批次和訓練輪數。
    一般而言， `max_batches`  最小6000，所以如果是一個class，則設置為6000。
    `steps = max_batches * 0.8, max_batches*0.9` 所以如果一個class，設置為 `steps=4800, 5400` 即可。

    註：具體什麽時候停止，可以在訓練過程中觀察 `avg_loss` 來決定。當 `avg_loss` 不再減小，則可以停止訓練。 

  - Step 4: [net] 下的 `width` 和 `height` 可以設置訓練圖片的分辨率。這裏設置的越大，耗時越長，可以根據實際訓練圖片素材來設定大小。但必須設置為32的倍數。比例 1:1，一般設置為 416 * 416。

  - Step 5: [net] 下的 `batch` 和 `subdivisions` ，由於是進行訓練，這裏不用修改。但如果訓練過程中，出現了 CUDA out of memory 的提示，可以將這裏的 `subdivisions` 增大，但這個數值越大，訓練用時越長，因此修改前務必進行權衡。

For `batch` and `subdivisions` , you can choose **16, 32, 64** for `batch` and **4, 8, 16** for `subdivisions`.



#### 修改 .names 文件和 .data 文件

- 隨便找一個 `.names` 文件，復製一份，改名為自己所需要的名字，例如 `remote.names` 
  這個文件是用於存儲要識別的物品類別的名字。每個名字一行。如果只有一個類，則該文件只有一行，如下所示：

```bash
remote
```

- 新建一個名為 `remote.data` 的文件用於存放關於訓練數據的一些內容，內容如下：

```bash
classes = 1               #訓練類別的數目
train = data\train.txt    #訓練集圖片的路徑(含義見第 0 部分)
valid = data\test.txt     #驗證集圖片的路徑
names = data\remote.names #訓練類別的名字
backup = backup\          #訓練輸出模型保存路徑 #如相對路徑不行，換成絕對路徑
```

⚠️ 如果用 Ubuntu 之類的 Linux 系統進行訓練，需要將 `\` 改為  `/` 來匹配系統路徑風格。

`.data` 文件中的 `.txt` 文件記錄了訓練、測試所用的圖片的位置。

- 在 `darknet\build\darknet\x64` 下打開 Powershell，利用如下命令進行訓練（在 PowerShell 中，可以不管 slash 的左右，但 cmd 可能要改）：

```bash
./darknet.exe detector train ./cfg/remote.data ./cfg/remote.cfg yolo-fastest.conv.109
```

視情況而定是否需要添加 `-dont_show` 參數。如果用沒有外接顯示器的服務器，則需要添加這個參數避免出現錯誤。

如果需要使用 GPU 進行訓練，需要在命令末尾加上 `-gpu` 指令。



#### 開始訓練時有關文件存放一覽

test 和 train 文件夾放在 darknet\build\darknet\x64\data 下。

remote.names，train.txt 和 test.txt 也放在 darknet\build\darknet\x64\data

yolo-fastest.conv.109 預訓練權重放在 darknet\build\darknet\x64 下。

remote.cfg 和 remote.data 文件放在 darknet\build\darknet\x64\cfg 下。



**文件存儲路徑預覽如下**：

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

