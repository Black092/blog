---
title: "dockerのインストール"
date: 2021-01-03T00:55:33+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "dockerの記録"
tags: [ docker ]
categories: [ docker ]
type: post
archives:
    - 2020

draft: false
---

# docker
# 実行環境
- OS : Ubuntu18.04
 
## 導入前のセッティング
- パッケージを更新
``` sh
$ sudo apt update
```

- 必要なパッケージのインストール
``` sh
$ sudo apt install \
         apt-transport-https \
         ca-certificates \
         curl \
         gnupg-agent \
         software-properties-common
```

- Docker 公式の GPG 公開鍵をインストール
``` sh
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

- fingerprintが(```9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88```)であることの確認
``` sh
$ sudo apt-key fingerprint 0EBFCD88
```

- repository (stable) の追加
``` sh
$ sudo add-apt-repository \
         "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
         $(lsb_release -cs) \
         stable"
```
## dockerの導入
- パッケージを更新
``` sh
$ sudo apt update
```

- 各種インストール
    - ```docker-ce```
        - docker本体。これは必ずインストール。
    - ```docker-ce-cli```
        - docker run や docker build などの Dockerコマンドを実行するコマンドラインツール。
    - ```containerd.io```
        - コンテナランタイムのやつ。
``` sh
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

- dockerバージョン確認
``` sh
$ docker version
```

- Dockerデーモンが起動しているか確認
``` sh
$ sudo systemctl status docker
```
| 出力結果より抜粋 | Active: ```active (running)``` |
| ---------------- | ------------------------------ |

## dockerでHello world
- dockerを実行してみる
``` sh
$ sudo docker run hello-world
```

| 出力結果より抜粋 | Hello from Docker ! |
| ---------------- | ------------------- |
- dockerイメージの```ubuntu```をpullしてきてdockerシェルを起動する
``` sh
$ sudo docker run -it ubuntu
```
- ```root@<英数字>:/#```のような形でdockerシェルが起動する
 
- dockerシェルを閉じるとき
``` sh
$ exit
```

## sudoなしで実行できるようにする
- sudoがないと怒られる
``` sh
$ docker info
    
 # 出力結果
 # ERROR: Got permission denied while trying to connect to the Docker daemon socket at・・・
```

- 面倒なのでつけなくても実行できるようにする
``` sh
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
$ sudo systemctl enable docker
$ sudo systemctl start docker
```