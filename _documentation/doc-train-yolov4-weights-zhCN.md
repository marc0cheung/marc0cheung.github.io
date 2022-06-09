---
title: '在 Windows 上训练自己的 YOLOv4 权重文件'
date: 2022-06-08
permalink: /doc/2022/06/train-yolov4-weights-zhCN/
tags:
  - YOLOv4
  - YOLOv4 Weights
  - Tutorials
  - Windows
---





**选择语言**: [英文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights/) / [正体中文](https://marc0cheung.github.io/doc/2022/06/train-yolov4-weights-zhHK/) / 简体中文

首先要确定自己电脑上的 darknet 可以正常利用命令行工作，如果不可以，则参考 [部署 YOLOv4](https://marc0cheung.github.io/doc/2022/06/deploy-yolov4-windows-zhCN/) 部分的内容。



### 1. 训练前数据标记

- 获取某个目标的若干照片，例如500张包含遥控器的照片；
- 对所有的图片进行随机分类，分为训练集和测试集（二者的比例自行确定），例如  `train_remote_imgs` 以及 `test_remote_imgs` ；
- 使用 labelImg 标注工具对这些图片中的要识别的目标进行标记，标记的数据格式为 json 格式，即每张图片的 label 文件。（**如果是给 YOLOv4 ，则一定要预先选择YOLO格式，就不用转换了**；如果需要转换，可参见本文末尾收录的 `XML_to_YOLO.py` ）
- 利用 Python 代码（放置于末尾的 `Split_Data.py` ）将图片随机分为训练集和验证集，手工随机抽取也可以。但注意，训练集和测试集的图片是不能重复的。即不能同一张图片，既出现在训练集，又出现在测试集。
- 利用代码（本文末尾收录的 `XML_to_YOLO.py` ）将 XML 标注文件转换为 YOLO 支持的 txt 格式文件（如果提前在 LabelImg 中选择了 YOLO 格式则不需要做这个处理了，**所以一定要提前选格式！！**）

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-train-yolov4-weights.assets/ChooseCorrectFormat.png" alt="ChooseCorrectFormat" style="zoom:60%;" /></div>



### 2. 准备训练用的 .txt 文件

利用如下代码 ( `label2train.py` 文件) 生成 `train.txt` 和 `test.txt` 两个文件， `train.txt` 的内容是全部训练数据的路径，作为训练用；而 `test.txt` 的内容是全部测试集数据的路径，用作测试。

在生成 `.txt` 文件之前，需要将 `label2train.py` 放到恰当的位置执行。对应的训练素材文件结构目录如下图所示。

```
YOLOTraining (用于存放训练素材的文件夹)
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
|_____ test.txt (在调用 label2train.py 后生成)
|_____ train.txt (在调用 label2train.py 后生成)
```



`label2train.py` 代码如下：

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



⚠️ 注意在Windows中训练的路径符号应该是 `\` ，要注意生成的情况，避免出现错误。所以上述程序中专门做了系统类型切换。u 代表 unix 类，w 代表 Windows 风格。



### 3. 开始训练（以训练 YOLO-Fastest 网络为例）

- darknet 训练权重的 PowerShell 命令如下：

```bash
./darknet.exe detector train ./cfg/xxx.data ./cfg/xxx.cfg yolov4-tiny.conv.29 -cpu

# 或者 -gpu 利用显卡训练模型
```



最后的 `.conv.xxx` 格式文件，是一个称为 `预训练权重` 的东西，是训练自己的网络所必须的文件。有些 `预训练权重` 可以在网络上下载到，但有些网络上无法找到。此时需要自己通过指令生成。

#### 生成预训练权重的方法

- 进入编译后得到的 darknet 的文件夹，例如 `D:\darknet\build\darknet\x64` ，在该路径的 PowerShell 下键入如下指令：

```bash
.\darknet.exe partial Yolo-Fastest\COCO\yolo-fastest-1.1-xl.cfg Yolo-Fastest\COCO\yolo-fastest-1.1-xl.weights yolo-fastest.conv.109 109
```

-  可以在 x64 文件夹下，得到一个名为 `yolo-fastest.conv.109` 的 COCO 预训练模型 文件。



#### 修改 .cfg 文件

- 把 `yolo-fastest-1.1-x1.cfg` 复制一份，改名为自己需要的名字，例如 `remote.cfg` 
  用文本编辑器打开，对这个文件进行修改。修改步骤如下所示：

  - Step 1: 修改 classes = `自己训练集中包含的目标类别数` ，例如，需要网络去识别 `遥控器` 和 `鼠标` ，那么这个值就是 `2` 。把 `.cfg` 文件中能搜索到的所有 `classes` 值都改了。

  - Step 2: 修改 [yolo] 上一层 [convolutional] 里的 `filters = 3 * (class_num + 5)=18` ，这里的 `class_num` 是  `自己训练集中包含的目标类别数` 。也是把所有 [yolo] 上方的 [convolutional] 都改掉。

  - Step 3: 更改 `max_batches` 和 `steps` ，也就是训练批次和训练轮数。
    一般而言， `max_batches`  最小6000，所以如果是一个class，则设置为6000。
    `steps = max_batches * 0.8, max_batches*0.9` 所以如果一个class，设置为 `steps=4800, 5400` 即可。

    注：具体什么时候停止，可以在训练过程中观察 `avg_loss` 来决定。当 `avg_loss` 不再减小，则可以停止训练。 

  - Step 4: [net] 下的 `width` 和 `height` 可以设置训练图片的分辨率。这里设置的越大，耗时越长，可以根据实际训练图片素材来设定大小。但必须设置为32的倍数。比例1:1，一般设置为 416 * 416。

  - Step 5: [net] 下的 `batch` 和 `subdivisions` ，由于是进行训练，这里不用修改。但如果训练过程中，出现了 CUDA out of memory 的提示，可以将这里的 `subdivisions` 增大，但这个数值越大，训练用时越长，因此修改前务必进行权衡。

对于 `batch` 和 `subdivisions` , 你可以选择 **16, 32, 64** for `batch` 以及 **4, 8, 16** for `subdivisions`.



#### 修改 .names 文件和 .data 文件

- 随便找一个 `.names` 文件，复制一份，改名为自己所需要的名字，例如 `remote.names` 
  这个文件是用于存储要识别的物品类别的名字。每个名字一行。如果只有一个类，则该文件只有一行，如下所示：

```bash
remote
```

- 新建一个名为 `remote.data` 的文件用于存放关于训练数据的一些内容，内容如下：

```bash
classes = 1               #训练类别的数目
train = data\train.txt    #训练集图片的路径(含义见第 0 部分)
valid = data\test.txt     #验证集图片的路径
names = data\remote.names #训练类别的名字
backup = backup\          #训练输出模型保存路径 #如相对路径不行，换成绝对路径
```

⚠️ 如果用 Ubuntu 之类的 Linux 系统进行训练，需要将 `\` 改为  `/` 来匹配系统路径风格。

`.data` 文件中的 `.txt` 文件记录了训练、测试所用的图片的位置。

- 在 `darknet\build\darknet\x64` 下打开 Powershell，利用如下命令进行训练（在 PowerShell 中，可以不管 slash 的左右，但 cmd 可能要改）：

```bash
./darknet.exe detector train ./cfg/remote.data ./cfg/remote.cfg yolo-fastest.conv.109
```

视情况而定是否需要添加 `-dont_show` 参数。如果用没有外接显示器的服务器，则需要添加这个参数避免出现错误。

如果需要使用 GPU 进行训练，需要在命令末尾加上 `-gpu` 指令。



#### 开始训练时有关文件存放一览

test 和 train 文件夹放在 darknet\build\darknet\x64\data 下。

remote.names，train.txt 和 test.txt 也放在 darknet\build\darknet\x64\data

yolo-fastest.conv.109 预训练权重放在 darknet\build\darknet\x64 下。

remote.cfg 和 remote.data 文件放在 darknet\build\darknet\x64\cfg 下。



**文件存储路径预览如下**：

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

allimgs = glob.glob('原始数据集/train/' + "/*.jpg")
allimgs = np.sort(allimgs)
np.random.seed(100)
np.random.shuffle(allimgs)

train_ratio = 0.9
train_num = int(len(allimgs) * train_ratio)

# 得到训练和验证数据集列表
img_list_train = allimgs[:train_num]
img_list_val = allimgs[train_num:]

# 创建文件夹
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

# 移动val数据到指定位置
for i in img_list_val:
    img_id = i.split('.')[0].split('/')[2]
    print(img_id)
    # jpg
    shutil.copy(i, img_val + img_id + '.jpg')
    # xml
    shutil.copy('原始数据集/label/' + 'new_' + img_id + '.xml', label_val + img_id + '.xml')

# 移动train数据到指定位置
for i in img_list_train:
    img_id = i.split('.')[0].split('/')[2]
    print(img_id)
    # jpg
    shutil.copy(i, img_train + img_id + '.jpg')
    # xml
    shutil.copy('原始数据集/label/' + 'new_' + img_id + '.xml', label_train + img_id + '.xml')
```



### XML_to_YOLO.py

```python
import xml.etree.ElementTree as ET
import os
from os import getcwd
from os.path import join
import glob

sets = ['train', 'test']  # 分别保存训练集和测试集的文件夹名称
classes = ['remote']  # 标注时的标签

'''
xml中框的左上角坐标和右下角坐标(x1,y1,x2,y2)
》》txt中的中心点坐标和宽和高(x,y,w,h)，并且归一化
'''

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
    in_file = open(data_dir + '/%s_annotations/%s.xml' % (imageset, image_id))  # 读取xml
    out_file = open(data_dir + '/%s_labels/%s.txt' % (imageset, image_id), 'w')  # 保存txt

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
        cls_id = classes.index(cls)  # 获取类别索引
        xmlbox = obj.find('bndbox')
        b = (float(xmlbox.find('xmin').text), float(xmlbox.find('xmax').text), float(xmlbox.find('ymin').text),
             float(xmlbox.find('ymax').text))
        bb = convert((w, h), b)
        out_file.write(str(cls_id) + " " + " ".join([str('%.6f' % a) for a in bb]) + '\n')

wd = getcwd()
print(wd)  # 当前路径

data_dir = 'F:/Postgraduate_time/label_fish'

for image_set in sets:
    image_ids = []
    for x in glob.glob(data_dir + '/%s_annotations' % image_set + '/*.xml'):
        print(x)
        image_ids.append(os.path.basename(x)[:-4])
    print('\n%s数量:' % image_set, len(image_ids))  # 确认数量
    i = 0
    for image_id in image_ids:
        i = i + 1
        convert_annotation(data_dir, image_set, image_id)
        print("%s 数据:%s/%s文件完成！" % (image_set, i, len(image_ids)))

print("Done!!!")
```

