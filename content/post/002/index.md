---
title: "hugoで技術ブログを作ってみた"
date: 2021-01-01T20:20:14+09:00
description: "KO-TETSU(こてつ)の技術メモ"
summary: "hugoでブログを作った際の記録"
tags: [ hugo , web ]
categories: [web]
type: post
archives:
    - 2020

draft: false
---

# 技術ブログ

2021年の記録用に自作のブログがほしいので**hugo**で作ってgithub pageで公開してみる。

## hugaとは
**hugo**はオープンソースの静的サイトジェネレータの1つである。

**hugo**でブログを作る利点としては、
- デザインはテンプレートを使える
- **markdown**にも対応しているので更新もしやすい
- 使うだけなら簡単
- **github page**とかで静的サイトとして使用できる

---
## 始め方
[公式のページ](https://gohugo.io/getting-started/installing)に**hugo**のインストール方法があるので、合ったOSのコマンドを使う。

- **Ubuntu**

``` sh
snap install hugo
```

## 雛形の作成
次に作業フォルダを作成し```hugo new site```コマンドで雛形を作成する。

- **雛形はこんな感じ**

``` 
hello-hugo-docs
├── archetypes
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes
```

## テーマのダウンロード
テーマについては[Themes | Hugo](https://themes.gohugo.io/)で選ぶことができる。

私はブログっぽい[hulga](https://themes.gohugo.io/hugo-theme-hulga/)を選んだ。


``` sh
git init && git submodule add https://github.com/wlh320/hugo-theme-hulga.git themes/hulga
```

**config.toml**を編集する。

``` toml
baseURL = "http://example.org/"
languageCode = "ja"
title = "My New Hugo Site"
theme = "hulga"
```

編集したらテーマが適応されているか確認する。

``` sh
hugo server -D

```
サーバーが起動するので[http://localhost:1313/フォルダ名](http://localhost:1313/)とかで確認しておく。

## 記事の追加
```hugo new``` コマンドで.mdファイルを追加する。

``` sh
hugo new post/first-post.md
```

content/postにfirst-post.mdが追加されるので編集していく。

``` markdown
---
title: "My First Post"
date: 2021-01-01T12:46:47+09:00
draft: false
tags: [web]
---
```
- **title** はタイトル
- **date** は作成日
- **draft** は下書き
- **tags** はタグ

どんな属性があるかは[Page Variables | Hugo](https://gohugo.io/variables/page/)に書いてある。

```hugo``` コマンドでビルドして[http://localhost:1313/フォルダ名/post](http://localhost:1313/)とかで確認しておく。

## github pageでの公開
github pageでの公開できるようにする。

config.tomlを編集してdocs内にHTMLが生成されるように設定を変更する。
``` toml
---
baseURL = "https://Black092.github.io/blog/"
languageCode = "ja"
title = "My New Hugo Site"
theme = "hulga"
publishDir = "docs"
canonifyurls = true
---
```

- baseURL は自分のgithubで形式はhttp://[username].githug.io/[repository]
- publishDir はhtmlがpublishされる場所
- canonifyurls はbaseurl を基点とした絶対パスにする

あとはgithubにpushしてgithub pageをONにしてDocs内を読むように設定する。

## おまけ：記事テンプレート
記事を作成したときに自動生成されるのはdate,draft,titleですが変更することもできます。
archetypes内にあるdefault.mdに自動生成されるテンプレートがあるので変更してみるといいです。


## 参考
- [さよならQiita、こんにちはhugo × github pages - motty's blog](https://anyblog.hatenablog.jp/entry/2020/06/21/153128)
- [Hugoで新規記事を作成するときのTips的なメモ - Qiita](https://qiita.com/n0bisuke/items/4701481c3bca4df81b0b)
- [hugo:front matterについて - バグったメモ欄](https://burumos.xyz/posts/hugo/hugo-front-matter/)