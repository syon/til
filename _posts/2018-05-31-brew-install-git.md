---
layout: post
title:  "[Mac]HomebrewでGitをインストールしてPathを通す"
date:   2018-05-31 22:00:00 +0900
categories: [Mac]
---

![](/til/static/img/posts/brew-install-git.png)


## やりたいこと

Mac 標準の Apple Git は維持したまま、Homebrew で新しい Git をインストールして使えるようにします。


## 実現方法

```
~
⟩ git --version
git version 2.14.3 (Apple Git-98)

~
⟩ which git
/usr/bin/git

~
⟩ brew update
Already up-to-date.

~
⟩ brew install git
Warning: git 2.17.1 is already installed, it's just not linked
You can use `brew link git` to link this version.

~
⟩ brew link --overwrite git
Linking /usr/local/Cellar/git/2.17.1... 188 symlinks created

~
⟩ git --version
git version 2.17.1

~
⟩ which git
/usr/local/bin/git
```

※ 場合によっては `/usr/local/bin/git` に別のバージョンの Git が設置されています。
このとき `brew link --overwrite git` で参照先を上書きします。
