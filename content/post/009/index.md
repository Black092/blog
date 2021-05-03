---
title: "cocosynthでcocoモデルを作る方法"
date: 2021-01-03T00:55:33+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "cocosynthでcocoモデルを作る方法"
tags: [ cocosynth ]
categories: [ 画像認識 ]
type: post
archives:
    - 2020

draft: false
---
# cocosynthでcocoモデルを作る方法
物体認識のためのcocoモデル作成。

## cocosynth
合成画像を使ってcocoモデルを作れるよってツール。

[Github:cocosynth](https://github.com/akTwelve/cocosynth)

## 必要なもの
- 適当な背景画像
- 認識したい(切り抜いた)物体画像

# 画像
画像についてはスマホまたは(Web)カメラなどであらかじめ撮影しておいてください。

手順としては、

1. 認識したい物体をいろんな角度から撮影。枚数は任意です。

2. 撮影できたらGIMPなどで物体のみ切り抜きをおこなってください。

3. 切り抜きが終われば任意のファイルを作成してください。今回はobjectsファイルとしました。画像は入力情報として扱うので、ファイル内にinputファイルを作成してください。inputファイル内に各物体ごと(例：books,boxes,bottlesなど)の名前をつけたファイルを用意してその中に画像を保存してください。 


# 作成環境
今回はubuntu18でAnacondaを使用した。

Getting Startedを見るとSetup方法が書いてある。

``` sh
git colne https://github.com/akTwelve/cocosynth

# 以下はLinux環境で実施
cd 
git colne https://github.com/akTwelve/cocosynth
conda create -n cocosynth python=3.6
conda activate cocosynth
conda install -c conda-forge shapely
cd cocosynth
pip install -r requirements.txt
```

環境構築できたらdetasetsフォルダ内に先ほどのobjectsファイルを突っ込みます。なので全体的な中身はこんな感じになっていると思います。

``` sh
.
├── datasets
│   ├── objects
│   │   └── input
│   │       ├── books
│   │       ├── bottles
│   │       └── boxes
│   └── README.md
├── docs
│   └── getting-started.md
├── LICENSE
├── notebooks
│   ├── coco_image_viewer.ipynb
│   └── train_mask_rcnn.ipynb
├── python
│   ├── coco_json_utils.py
│   └── image_composition.py
├── README.md
└── requirements.txt

9 directories, 9 files
```

## 合成画像とマスク画像の作成
次に以下のコマンドで背景と物体の合成画像とマスク画像の作成します。

countは作成する合成画像とマスク画像の枚数を表しています。

``` sh
cd ~/cocosynth/
conda activate cocosynth
python ./python/image_composition.py --input_dir ./datasets/objects/input --output_dir ./datasets/objects/output --count 100 --width 512 --height 512
```

# cocoモデルの作成
最後にcocoモデルを作成します。
``` sh
python ./python/coco_json_utils.py -md ./datasets/box_dataset_synthetic/output/mask_definitions.json -di ./datasets/box_dataset_synthetic/output/dataset_info.json
```


