---
layout: post
title: "Nuxt.jsのAxiosでwithCredentialsを有効にする"
date: 2020-06-17 23:00:00 +0900
categories: [Nuxt.js, Axios]
---


## やりたいこと

Nuxt.js の公式プラグイン `@nuxtjs/axios` を使ってAPI通信をするとき、
リクエストに Cookie を添えて送信するために withCredentials オプションを有効にしたい。


## 実現方法

- [Options | Axios Module #credentials](https://axios.nuxtjs.org/options.html#credentials)

__nuxt.config.js__
```js
  modules: [
    '@nuxtjs/axios',
    // ...
  ],
  axios: {
    baseURL: 'https://example.com/api',
    credentials: true
  },
```

- `baseURL`の指定が必要。[^1]
- `withCredentials` ではなく __`credentials`__ 。


## 補足: Nuxt.js Axios モジュールの利点

- Nuxt.js プラグインとして注入されたものを呼び出せばよいので import が不要
- Vuexストアでも this コンテキストから呼び出せる
- `$get`を使えば `result.data` の中身が得られる

```js
// In store
const ip = await this.$axios.$get('http://icanhazip.com')
```

- [Usage | Axios Module #Store Actions](https://axios.nuxtjs.org/usage.html#store-actions)


## 参考情報

- [axios withCredentials not being passed from nuxt.config.js · Issue #168 · nuxt-community/axios-module](https://github.com/nuxt-community/axios-module/issues/168)

[^1]: 厳密には [Options | Axios Module #baseURL](https://axios.nuxtjs.org/options.html#baseurl) を参照。
