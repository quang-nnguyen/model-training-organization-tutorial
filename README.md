# How to organize a model training repository - a tutorial

**Stage 0**: initial notebooks

**Stage 1**: 
* Split the initial notebooks into `/src/model.py`, `/src/dataset.py `, `/src/utils.py`, `train.py`, and `test.py` in the following basic structure.

```
├── src
│   ├── dataset.py
│   ├── model.py
│   └── utils.py
├── train.py
└── test.py
```

* Add `requirements.txt` for better installation.

**Stage 2**: split part of `train.py` to `/src/trainer.py`

`Note`: check commits in the branch to see the code progression through time.

### Installation
Create virtual environment
```bash
conda create -n myenv python=3.7
conda activate myenv
```

Install dependencies:
```bash
pip install -r requirements.txt
```

Download and set up data by running 
```bash
bash setup_data.sh
```

### Usage
Run 
```bash
python train.py
```

Expected output:
```
----- Loading data ... -----
Done
----- Loading model configuration ... -----
Done
----- Training model ... -----
Epoch 1/100 : |██----------| 23.26% [20/86 02:01<06:42 Training loss: 0.55536]
```

Make sure:
* the dataset follow the structure described below
* folders `models` (for training) and `results` (testing) exists

After the models are trained, run:
```bash
python test.py
```
for inference

### Test `model.py`
```python
from src.model import RetinaModel
net = RetinaModel(num_classes=8)
net
```

Expected Output:
```
RetinaModel(
  (backbone): Backbone(
    (net): ResNet(
      (conv1): Conv2d(3, 64, kernel_size=(7, 7), stride=(2, 2), padding=(3, 3), bias=False)
      (bn1): BatchNorm2d(64, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
      (relu): ReLU(inplace=True)
      (maxpool): MaxPool2d(kernel_size=3, stride=2, padding=1, dilation=1, ceil_mode=False)
      (layer1): Sequential(
        (0): Bottleneck(
    ...
      (avgpool): AdaptiveAvgPool2d(output_size=(1, 1))
      (fc): Sequential(
        (0): Linear(in_features=2048, out_features=8, bias=True)
        (1): Sigmoid()
      )
    )
  )
)
```

### Basic structure of Dataset class
```python
class RetinaDataset(Dataset):
    def __init__(self, folder_dir, dataframe, image_size, normalization=True):
        pass
    
    def __len__(self):
        pass

    def __getitem__(self, index):
        pass
```

### Structure of dataset
Folder `dataset` containing trianing images and corresponding labels.

```
├── dataset
|   ├── train/
|   │   ├── ff1de7000016.jpg
|   │   ├── ff2030d64169.jpg
|   │   ├── ff458fc77afc.jpg
|   │   ├── ff45db512719.jpg
|   │   ├── ff4b222612eb.jpg
|   │   ├── ...
|   └── train.csv
```

Structure of the label file
```
1 filename          opacity  diabetic retinopathy  glaucoma  macular edema  macular degeneration  retinal vascular occlusion  normal
2 c24a1b14d253.jpg  0        0                     0         0              0                     1                           0
3 9ee905a41651.jpg  0        0                     0         0              0                     1                           0
4 3f58d128caf6.jpg  0        0                     1         0              0                     0                           0
5 4ce6599e7b20.jpg  1        0                     0         0              1                     0                           0
6 0def470360e4.jpg  1        0                     0         0              1                     0                           0
7 e80c3ba691f9.jpg  1        0                     0         1              0                     1                           0
8 37b8fa3b6dce.jpg  1        0                     0         1              0                     1                           0
9 b5740f9b3508.jpg  1        0                     0         1              0                     1                           0  
```

### Test `dataset.py`
```python
import pandas as pd
import os
from src.dataset import RetinaDataset
DATA_DIR = './data'
IMAGE_SIZE = 224
labels = pd.read_csv(os.path.join(DATA_DIR, 'train.csv'))
dataset = RetinaDataset(os.path.join(DATA_DIR, 'train'), labels, (IMAGE_SIZE, IMAGE_SIZE), True)
image, label, _ = dataset[10]
```