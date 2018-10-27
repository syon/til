---
layout: post
title: "Nuxt.jsでHTML要素にlang属性をつける"
date:   2018-10-27 13:00:00 +0900
categories: [JavaScript, Vue.js, Nuxt.js]
---

![](/til/static/img/posts/nuxt-html-attrs.png)


## やりたいこと

Nuxt.js で HTML要素 `<html>` に lang 属性をつけて `<html lang="ja">` にする


## 実現方法

Nuxtコンフィグで全体適用する場合と、ページ単体に適用する方法を紹介します。
`head` の中に書くのがポイントです。

__nuxt.config.js__

```js
const pkg = require('./package')

module.exports = {
  mode: 'spa',

  /*
  ** Headers of the page
  */
  head: {
    title: pkg.name,
    htmlAttrs: {
      lang: 'ja'
    },
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: pkg.description }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
  // ...
}
```

__pages/sample.vue__

```html
<template>
</template>

<script>
export default {
  head() {
    return {
      htmlAttrs: {
        lang: 'ja'
      },
    }
  }
}
</script>
```
