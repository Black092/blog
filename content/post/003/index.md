---
title: "Ubuntu18でRealSense D435からpython3を使ってカメラ画像を取得する"
date: 2021-01-04T13:30:37+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "RealSenseの深度カメラでRGBとDepth画像を保存するpython3のコードを作る。"
author: "こてつ"
tags: [RealSense,python3,opencv]
categories: [画像処理]
type: post
archives:
    - 2020

draft:  false
---

# はじめに
RealSenseを使うときのTips記事。



# Ubuntu18.04 + RealSenseの環境構築
必要そうなものをいれておく。

``` sh
sudo apt-get install git cmake doxygen libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev
``` 

githubからクローンしてきてセットアップ。
``` sh
git clone https://github.com/IntelRealSense/librealsense
cd librealsense
./script/setup_udev_rules.sh
./scripts/patch-ubuntu-kernel-4.16.sh
```

cmakeしてビルドする。
``` sh
cmake ../ -DBUILD_EXAMPLES=true \
-DBUILD_GRAPHICAL_EXAMPLES=true \
-DBUILD_PYTHON_BINDINGS=bool:true 
make
sudo make install
sudo ldconfig
```

realsense-viewerが開いてカメラ画像が出れば成功。
``` sh
realsense-viewer
```

![realsense-viewer](/post/002/images/realsense-viewer.png)

---

でもRGBとdepth画像をよくみると視差があるし、保存するのもボタン押さないといけないのでめんどう。

なのでpythonを使ってきちんと保存してくれるスクリプトを作成する。

## セットアップ
---

### pyrealsense2
``` sh
sudo apt install python3-pip
pip3 install pyrealsense2
```

### numpy
``` sh
pip3 install numpy --upgrade
```

### opencv2
``` sh
pip3 install opencv-contrib-python
```

#### color画像とdepth画像を表示するスクリプト

それぞれカラー画像と深度カメラ画像を表示してみる。

``` python
import pyrealsense2 as rs
import numpy as np
import cv2

IM_WIDTH = 640
IM_HEIGHT = 480
FPS = 30
ESC = 27

# ストリーム(Depth/Color)の設定
config = rs.config()

config.enable_stream(rs.stream.color, IM_WIDTH, IM_HEIGHT, rs.format.bgr8, FPS)
config.enable_stream(rs.stream.depth, IM_WIDTH, IM_HEIGHT, rs.format.z16, FPS)

# ストリーミング開始
pipeline = rs.pipeline()
profile = pipeline.start(config)

try:
    while True:

        # フレーム待ち(Color & Depth)
        frames = pipeline.wait_for_frames()

        color_frame = frames.get_color_frame()
        depth_frame = frames.get_depth_frame()
        if not depth_frame or not color_frame:
            continue

        #imageをnumpy arrayに
        color_image = np.asanyarray(color_frame.get_data())
        depth_image = np.asanyarray(depth_frame.get_data())


        cv2.namedWindow('RealSense:color_image', cv2.WINDOW_AUTOSIZE)
        cv2.imshow('RealSense:color_image', color_image)
        cv2.namedWindow('RealSense:color_depth', cv2.WINDOW_AUTOSIZE)
        cv2.imshow('RealSense:color_depth', depth_image)
        if cv2.waitKey(1) & 0xff == ESC:#ESCで終了
            cv2.destroyAllWindows()
            print('finish')
            break

finally:

    #ストリーミング停止
    pipeline.stop()
```
画像が表示されたと思う。ウィンドウをクリックしてESCで終了できる。

---
次に画角が違うのでAlignオブジェクトを使って画角を揃える。あと画像の保存とかもできると楽なので付け加える。

#### 画角を揃えてSキーで保存ができるようなスクリプト
``` python
import pyrealsense2 as rs
import numpy as np
import cv2

IM_WIDTH = 640
IM_HEIGHT = 480
FPS = 30
capture_count = 0
ESC = 27

# ストリーム(Depth/Color)の設定
config = rs.config()

config.enable_stream(rs.stream.color, IM_WIDTH, IM_HEIGHT, rs.format.bgr8, FPS)
config.enable_stream(rs.stream.depth, IM_WIDTH, IM_HEIGHT, rs.format.z16, FPS)

# ストリーミング開始
pipeline = rs.pipeline()
profile = pipeline.start(config)

# Alignオブジェクト生成
align_to = rs.stream.color
align = rs.align(align_to)

try:
    while True:

        # フレーム待ち(Color & Depth)
        frames = pipeline.wait_for_frames()
        aligned_frames = align.process(frames)

        color_frame = aligned_frames.get_color_frame()
        depth_frame = aligned_frames.get_depth_frame()
        if not depth_frame or not color_frame:
            continue

        #imageをnumpy arrayに
        color_image = np.asanyarray(color_frame.get_data())
        depth_image = np.asanyarray(depth_frame.get_data())


        cv2.namedWindow('RealSense:color_image', cv2.WINDOW_AUTOSIZE)
        cv2.imshow('RealSense:color_image', color_image)
        cv2.namedWindow('RealSense:color_depth', cv2.WINDOW_AUTOSIZE)
        cv2.imshow('RealSense:color_depth', depth_image)

        if cv2.waitKey(1) & 0xff == ESC:#ESCで終了
            cv2.destroyAllWindows()
            print('finish')
            break
        elif cv2.waitKey(1) & 0xff == ord('s'):
            cv2.imwrite("images/" + str(capture_count) + "color_image.png", color_image)
            cv2.imwrite("images/" + str(capture_count) + "depth_image.png", depth_image)
            print('save images')
            capture_count += 1

finally:

    #ストリーミング停止
    pipeline.stop()
```


# 参考
- [Pythonではじめる3Dセンシング!! - みらいテックラボ](https://mirai-tec.hatenablog.com/entry/2018/07/29/150902)
- [Ubuntu18.04 + RealSenseの環境構築 - ロボットシステムデザイン研究室](http://www1.meijo-u.ac.jp/~kohara/cms/technicalreport/ubuntu1804_realsense)
- [RealSence D435をPythonで使う【画角を合わせる編】（Windows10）- Qiita](https://qiita.com/tom_eng_ltd/items/635414ff0b43e1c506f6)