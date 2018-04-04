---
layout: post
title:  "Google検索のリダイレクト先をJPに向ける"
date:   2018-04-04 22:00:00 +0900
categories: [Node.js, AWS Lambda]
---

## やりたいこと

日本のインターネット環境からブラウザで以下のURLにアクセスすると

- https://www.google.com/?q=わーい

こんな感じのURLにリダイレクトされる。

- `https://www.google.co.jp/?q=%E3%82%8F%E3%83%BC%E3%81%84&gws_rd=cr&dcr=0&ei=-dnEWqDECIHK8wXs3YbYBg`

挙動から察するに、おそらくブラウザの言語設定を検知して振り分けている。
聞いた話ではGoogleアカウントに検索用の言語設定をできるようだけど、
本件ではスクレイピング／クローリング用途のためログインができることを前提としない。

AWS Lambda に置いた HTTP クライアント、
例えば [npm の `request` モジュール](https://www.npmjs.com/package/request) などで
間接的にアクセスする場合、東京リージョンであっても .co.jp
にリダイレクトされない状況に出くわした。
これを日本向けにリダイレクトされるようにしたい。


## 実現方法

リクエストヘッダに __Accept-Language__ を指定する。

```js
import request from "request-promise-native";

const opts = {
  url,
  headers: {
    // :
    "Accept-Language": "ja,en-US;q=0.9,en;q=0.8",
    // :
  }
};
await request.get(opts).then(/**/)
```
