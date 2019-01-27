---
layout: post
title: "MacのターミナルでAlt・Optionキーをメタキーとして使う"
date: 2019-01-27 12:00:00 +0900
categories: [Mac, Terminal]
---

![](/til/static/img/posts/terminal-metakey.jpg)

## やりたいこと

[fzf](https://github.com/junegunn/fzf) を導入し、デフォルトのキーバインドに ALT-C があった。

Macのターミナルで <kbd>alt + C</kbd> を打ち込むと __ç__ が入力されてしまい、キーバインドを反応させられない。

Mac の alt / option キーの挙動を変更したい。


## 実現方法

ターミナル.app > 環境設定 > プロファイル > キーボード > ☑メタキーとしてOptionキーを使用

![](/til/static/img/posts/terminal-pref.jpg)
