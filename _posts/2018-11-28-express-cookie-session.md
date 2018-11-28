---
layout: post
title: "【解決】req.session.touch is not a function"
date: 2018-11-28 21:00:00 +0900
categories: [Node.js, Express]
---

## やりたいこと

Node.js Express にて、npm の `cookie-session` を使ってセッションを管理する。


## エラー

```bash
        req.session.touch()
                    ^

TypeError: req.session.touch is not a function
```

> TypeError: req.session.touch is not a function

本来ミドルウェアが session.touch を関数として呼べるはずであるが、備わっていなかった。
何らかの原因によって上書き・改変され失われたと考えられる。


## 原因

```js
const session = require('express-session');
const cookieParser = require('cookie-parser');
const cookieSession = require('cookie-session')

// :
app.use(session({ resave: true, saveUninitialized: true, secret: 'uwotm8' }));
app.use(cookieParser());
app.use(cookieSession({/* ... */}));
// :
```

検証に利用していたソースコードが Express 4 のサンプルであり、
すでに `express-session` というセッション用のミドルウェアを利用していた。


## 解決方法

```js
// const session = require('express-session');
const cookieParser = require('cookie-parser');
const cookieSession = require('cookie-session')

// :
// app.use(session({ resave: true, saveUninitialized: true, secret: 'uwotm8' }));
app.use(cookieParser());
app.use(cookieSession({/* ... */}));
// :
```

`express-session` を除去。

なお今回は問題とならなかったが、`cookieParser` と `cookieSession` の app.use を実行する順番も関係してくるかもしれない。


## 参考記事

- [node.js - req.session has no method 'touch'? - Stack Overflow](https://stackoverflow.com/questions/27592423/req-session-has-no-method-touch)
  - この記事から気づきを得た。別のセッション関連ミドルウェアが影響を与えている。
- [ExpressJS Session Error: req.session.touch() is not a function](https://davidburgos.blog/expressjs-session-error-req-session-touch-not-function/)
  - 似ているけど本件とは関係ないように見える
