---
layout: post
title:  "Fisherman (fish shell) のアップグレード"
date:   2018-03-30 21:00:00 +0900
categories: [Mac, Shell]
---

fish shell のプラグインマネージャ Fisherman (command: `fisher`)
それ自体をアップグレードするには、公式ドキュメントのインストールコマンドを
再度実行することで達成できた。

```bash
# 最新版を再インストール
$ curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs https://git.io/fisher
```

```bash
# バージョン確認
$ fisher -v
fisherman version 2.13.2 ~/.config/fish/functions/fisher.fish
```

ひと通り情報を探し回ったけど、明確にその答えを指し示す情報源は見つからなかった。
`fisher help` にも記載は見当たらなかった。

- https://github.com/fisherman/fisherman
