---
title: "CUDAとcuDNN(GPU付きUbuntuデスクトップ)"
date: 2021-01-03T00:55:33+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "GPU付きUbuntuデスクトップにCUDAを入れた話"
tags: [ セットアップ,GPU,CUDA]
categories: [ セットアップ ]
type: post
archives:
    - 2020

draft: false
---


# NVIDIA GPU
NVIDIAのドライバー、CUDA、cuDNNのメモ

# 環境
- OS : ubuntu18.04
- GPU : RTX1070

# 事前準備
## すでに入っているか確認する
- ドライバーの確認
``` sh
$ ubuntu-drivers list
```

- cudaの確認
``` sh
$ dpkg -l | grep cuda
```
- cuDNNの確認
``` sh
$ dpkg -l | grep cudnn
```

## すでに入っているの削除する
- ドライバーの削除
``` sh
$ sudo apt-get --purge remove nvidia-*
$ sudo apt-get --purge remove libnvidia-*
```

- cudaの削除
```sh 
$ sudo apt-get --purge remove cuda-*
$ sudo apt-get --purge remove libcuda-*
```

- cuDNNの削除
``` sh
$ sudo apt-get --purge remove cudnn-*
$ sudo apt-get --purge remove libcudnn-*
```

- 上記のいずれかの削除をしたら以下のコマンドを実行しておく。
``` sh
$ sudo apt-get autoremove
```

- 念の為ドライバーが認識されているかどうか試してみる。
``` sh
$ nvidia-smi
```
- ```nvidia-smi```をコマンド入力してもないよって言われる状態になるはず。

# インストール編
## ドライバーをいれる
- リポジトリの追加
``` sh
$ sudo add-apt-repository ppa:graphics-drivers/ppa
$ sudo apt update
```

- インストール推奨Driverを確認
``` sh
$ ubuntu-drivers devices
```
- 参考：推奨かどうかは出力内容に```recommended``` があるかどうかを確認する

| 出力結果より抜粋 | driver : ```nvidia-driver-455``` - third-party free ```recommended``` |
| ------------ | --------------------------------------------------------- |

- ```nvidia-driver-455```をインストール
``` sh
$ sudo apt install nvidia-driver-455
```

- 再起動
``` sh
$ sudo reboot
```

- ドライバーが認識されているか確認する。
``` sh
$ nvidia-smi
```
| 出力内容より抜粋 | NVIDIA-SMI 455.45.01 Driver Version: 455.45.01 CUDA Version: 11.1 |
| ---------------- | ----------------------------------------------------------------- |
 
## CUDA
今回は試しにCUDA10.1をいれてみる。
- CUDAについては[cuda-toolkit](https://developer.nvidia.com/cuda-toolkit-archive)から選択してダウンロード。


| 項目 | 選択肢                                                                                                                                                                                     |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Operating System | Linux                                                                                                                                                                                     |
| Architecture     | x86_64                                                                                                                                                                                    |
| Distribution     | Ubuntu                                                                                                                                                                                    |
| Version          | 18.04                                                                                                                                                                                     |
| Installer Type   | deb(local)                                                                                                                                                                                |
| URL              | [上記の内容を入力したもの](https://developer.nvidia.com/cuda-10.1-download-archive-base?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal) |
- サイトのDownloadからdebをダウンロードする。
- サイトの```Installation Instructions:```にコマンド入力方法があるのでそちらを参考に実行していく。
``` sh
$ sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.105-418.39_1.0-1_amd64.deb
```
- 以下の```<version>```は上記の```10-1-local-10.1.105-418.39```の部分を入力する。
``` sh
$ sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub`
$ sudo apt-get update
```
- 本家は```cuda```だけどバージョンわかりやすいように```cuda-10.1```に変更する。
``` sh
$ sudo apt-get install cuda-10.1
```

- ```/usr/local/```に```cuda-10.1```が入っているか確認する。
``` sh
$ cat /usr/local/cuda-10.1
```

## cuDNN
- cuDNNについてはcudaのバージョンを考慮しながら[cuDNN](https://developer.nvidia.com/rdp/cudnn-archive)からダウンロードしてくる。
- 項目については```Download cuDNN v7.6.5 (November 5th, 2019), for CUDA 10.1```を選択して以下の３つをダウンロード。
    - cuDNN Runtime Library for Ubuntu18.04 (Deb)
    - cuDNN Developer Library for Ubuntu18.04 (Deb)
    - cuDNN Code Samples and User Guide for Ubuntu18.04 (Deb)
- ダウンロード時はログインが必要になるので[Temp mail](https://temp-mail.org/ja/)とかで適当にアカウントつくるのもあり。
- ダウンロードしてきたら次のコマンドを実行する。

``` sh
$ sudo dpkg -i libcudnn7_7.6.5.32-1+cuda10.1_amd64.deb
$ sudo dpkg -i libcudnn7-dev_7.6.5.32+cuda10.1_amd64.deb
$ sudo dpkg -i libcudnn7-dec_7.6.5.32+cuda10.1_amd64deb
```

## PATHの設定
- pathの設定のために以下のコマンドを実行する。
``` sh
$ echo -e "\n## CUDA and cuDNN paths" >> ~/.bashrc
$ echo 'export PATH=/usr/local/cuda-10.1/bin:${PATH}' >> ~/.bashrc
$ echo 'export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64:${LD_LIBRARY_PATH}' >> ~/.bashrc
```

## CUDAとcuDNNのバージョン確認
- CUDAのバージョン
``` sh
$ nvcc -V
```

- cuDNNのバージョン
``` sh
$ cat /usr/include/cudnn.h | grep CUDNN_MAJOR -A 2
```

# cudaを入れるドライバーが認識されなくなるとき
- cudaを入れるとGPUドライバーが認識されなくなるときがあります。

## 参考
- [nvidia-smi で Failed to initialize NVML: Driver/library version mismatch と言われたとき【GPU】](https://qiita.com/ell/items/be3d3527b723f70f888d)を参考にしてみてください。

``` sh
$ nvidia-smi

# 出力結果
# Failed to initialize NVML: Driver/library version mismatch

$ sudo rmmod nvidia_drm
$ sudo rmmod nvidia_modeset
$ sudo rmmod nvidia_uvm

```
## sudo rmmod nvidia_drmできないとき
- ドライバーのアンロード

``` sh
$ systemctl stop gdm
$ systemctl isolate multi-user.target
$ systemctl stop systemd-logind
$ killall gdm-x-session
```

- 再実行
``` sh
$ sudo rmmod nvidia_drm
$ sudo rmmod nvidia_modeset
$ sudo rmmod nvidia_uvm
```

- nvidiaをアンロードしてnvidia-smiする
``` sh
$ sudo rmmod nvidia
$ nvidia-smi
```


# おまけ

## CUDAバージョンの決め方
- Tensorflow-GPUやpytorchを使う場合はCUDAのバージョンを確認する必要がある。
    - [Tensorflow-GPU](https://www.tensorflow.org/install/source#tested_build_configurations)
    - [PyTorch](https://pytorch.org/get-started/previous-versions/)
