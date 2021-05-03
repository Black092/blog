---
title: "condaコマンド"
date: 2021-01-03T00:55:33+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "condaコマンド"
tags: [ python3,conda ]
categories: [ conda ]
type: post
archives:
    - 2020

draft: false
---

# Anacondaダウンロード
Anaconda(https://www.anaconda.com/products/individual)

# よく使うコマンド
``` sh
# 環境作成
conda create -n [name] python=[version] [library]

# 環境確認
conda info -e

# 環境に入る
conda activate [name]

# 環境から出る
deactivate [name]

# 環境削除
conda remove -n [name] --all
```

# 端末に出る(base)を消す方法
いつも(base)が出るようになるので消す。
``` sh
conda config --set auto_activate_base False
```

使うときはconda activateすると使える。