---
layout: post
title: "Nuxt.jsでIEの互換表示を無効にする"
date:   2019-08-24 16:00:00 +0900
categories: [JavaScript, Vue.js, Nuxt.js]
---

![](/til/static/img/posts/nuxt-ie-edge.png)


## やりたいこと

Nuxt.js ですべてのページを IE11 に対応するために、互換表示モードを無効にする以下のメタタグを出力させたい。

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
```


## 実現方法

Nuxtコンフィグに追記します。HTMLで指定する内容をJSとして情報を与えるため、以下のようになります。
`http-equiv` にハイフンが含まれているため、シングルクォーテーションで囲む必要がある点に注意です。

__nuxt.config.js__ (v2.9.1)

```js
export default {
  mode: 'universal',
  /*
   ** Headers of the page
   */
  head: {
    title: process.env.npm_package_name || '',
    meta: [
      { charset: 'utf-8' },
      { 'http-equiv': 'X-UA-Compatible', content: 'IE=edge' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      {
        hid: 'description',
        name: 'description',
        content: process.env.npm_package_description || ''
      }
    ],
    link: [{ rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }]
  },
  // ...
}
```
