---
title: "ubuntu18でopenposeを使う方法"
date: 2021-01-03T00:55:33+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "ubuntu環境でopenposeを動かそうとした際の記録"
tags: [ openpose ]
categories: [ 画像認識 ,openpose]
type: post
archives:
    - 2020

draft: false
---

# openpose
openposeは人物の骨格推定などができるもの。
使うのは本家のcaffe版の方でGPUあり版となし版があるがあり版の解説。

## github
公式のgithubは[こちら](https://github.com/CMU-Perceptual-Computing-Lab/openpose)

# 事前準備

## 必要なライブラリのインストール
- openCVなどのインストール
``` sh
$ sudo apt-get install \
     qtbase5-dev \
     build-essential \
     gdebi \
     libopencv-dev
```

## cmakeのインストール
- 古いcmakeの削除
``` sh
$ sudo apt purge cmake-qt-gui
```

- 必ず最新のcmakeをダウンロードしてくる

    - cmakeの[ダウンロード](https://cmake.org/download/)

- もしくは以下のコマンドでも可
``` sh
$ wget https://github.com/Kitware/CMake/releases/download/v3.13.4/cmake-3.13.4.tar.gz
$ tar xzvf cmake-3.13.4.tar.gz    
```

- 移動してcakeをコンパイルとかしてインストールする
``` sh
$ cd cmake-3.13.4
$ ./configure --qt-gui
$ ./bootstrap
$ make -j6
$ sudo make install
```

# openposeのインストール
## 作業フォルダの作成
- openpose_wsというフォルダ(任意)を作ってcloneしてくる
``` sh
$ mkdir openpose_ws
$ cd openpose_ws
$ git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose
$ cd openpose
```

- 必要なものをインストールしてくれる
- エラーが出ても特にopenposeそのものに関係したものではないので無視
``` sh
$ sudo bash ./scripts/ubuntu/install_deps.sh
    
# たぶんこんな感じのエラーが出るけど無視
# Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-rfjg8m_p/opencv-python/
```

- build先のフォルダを作っておく
``` sh
$ cd ~/openpose_ws/openpose/
$ mkdir build
$ cd build
```


## 3rdpartyの取得
- caffeの取得
``` sh
$ cd ~/openpose_ws/openpose/3rdparty
$ git clone https://github.com/CMU-Perceptual-Computing-Lab/caffe.git
```

- pybindの取得
``` sh
$ git clone https://github.com/pybind/pybind11.git
```

## cmakeでビルド
- buildフォルダに移動してcmakeする
``` sh
$ cd ~/openpose_ws/openpose/build
$ cmake -DBUILD_PYTHON=true ..
$ make -j6
$ sudo make install
```

# 参考
- [OpenPoseで姿勢解析環境のセットアップ Ubuntu18.04LTS](https://qiita.com/myoshimi/items/cf64c91cd22c516bb49b)