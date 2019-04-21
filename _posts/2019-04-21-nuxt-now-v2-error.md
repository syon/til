---
layout: post
title: "Nuxt.js Universal on ZEIT Now v2"
date: 2019-04-21 20:00:00 +0900
categories: [Nuxt.js]
---

## やりたいこと

Nuxt.js の Universal モードを ZEIT Now の v2 で動かしたい。

現時点の `$ npx create-nuxt-app` で入る Nuxt のバージョンは `2.4.0` だったので、これは latest の `2.6.2` とする。


## 実現方法

Nuxt の公式リポジトリにあったこれを使う。初回リリースが2019年2月24日であり、ごく最近できた模様。

- [nuxt/now-builder: Now Builder for Nuxt.js](https://github.com/nuxt/now-builder)

__now.json__ を以下のように記述するだけ。インストールなど不要。

```json
{
  "version": 2,
  "builds": [
    {
      "src": "nuxt.config.js",
      "use": "@nuxtjs/now-builder",
      "config": {}
    }
  ],
  "routes": [
    { "src": "/_nuxt/.+", "headers": { "cache-control": "s-maxage=31536000" } },
    { "src": "/(.*)", "dest": "/" }
  ]
}
```

## エラー

![](/til/static/img/posts/now502.png)

> 502: An error occurred with your deployment
> Code: NO_STATUS_CODE_FROM_LAMBDA (more)

↓ Now 上のログ

```bash
Unable to import module 'now__launcher': Error
Error: Cannot find module './package'
Require stack:
- /var/task/nuxt.config.js
- /var/task/now__launcher.js
- /var/runtime/node_modules/awslambda/index.js
    at Object.<anonymous> (/var/task/nuxt.config.js:1)
```

`NO_STATUS_CODE_FROM_LAMBDA` はただの表面的な原因。内部的に使っている Lambda 上で起きているだけで、Lambda のせいではないと推測。
サーバ処理中に起きたエラーをキャッチできずにいたためレスポンスを返せなかったってところかな。


## 対処方法

[公式のサンプル](https://github.com/nuxt/now-builder/tree/master/examples/basic)は動作することを確認したので、その差を詰めていったところ、ここに原因があった。
また、上記エラーメッセージの最後にも`(/var/task/nuxt.config.js:1)`とある。1行目で `Error: Cannot find module './package'` とのこと。

![](/til/static/img/posts/now502-solve.png)

なぜこれがダメなのかは不明。直接書き込めばいいので不要な参照は無くし、問題は解消した。
