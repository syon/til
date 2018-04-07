---
layout: post
title:  "Chromelessでselectボックスを操作する"
date:   2018-04-07 20:00:00 +0900
categories: [Node.js, AWS Lambda]
---

## やりたいこと

Chromeless を使ってセレクトボックスの値を選択したい。
しかしそのためのAPIがない。

- [graphcool/chromeless: 🖥 Chrome automation made simple. Runs locally or headless on AWS Lambda.](https://github.com/graphcool/chromeless)


## 実現方法

`evaluate` でページに入り込み、対象要素を掴んでインデックスまたは値を指定して選択状態にする。

- [evaluate(fn: (...args: any[]) => void, ...args: any[]): Chromeless](https://github.com/graphcool/chromeless/blob/master/docs/api.md#evaluatefn-args-any--void-args-any-chromeless)

```js
const { Chromeless } = require("chromeless");

async function run() {
  const chromeless = new Chromeless();

  await chromeless
    .goto("https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select")
    .scrollToElement("#Result");

  await chromeless.wait(3000);

  await chromeless.evaluate((selector, index) => {
    select = document.querySelector(selector);
    select.selectedIndex = index;
  }, '[name="select"]', 0);

  await chromeless.wait(3000);

  await chromeless.evaluate((selector, value) => {
    select = document.querySelector(selector);
    select.value = value;
  }, '[name="select"]', "value3");

  await chromeless.wait(5000);
  await chromeless.end();
}

run().catch(console.error.bind(console));
```
