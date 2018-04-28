---
layout: post
title:  "[Node.js]yamlファイルのリストをTSVに変換する"
date:   2018-04-28 22:00:00 +0900
categories: [Node.js]
---

## やりたいこと

以下のようなファイル構造で、YAMLファイルからTSVを生成したい。
ただし、YAMLのプロパティから memo は除外したい。

```
actress
  `- 1
  |   `- profile.yaml
  `- 2
      `- profile.yaml
```

```yaml
name: 宮崎 あおい
kana: みやざき あおい
birthday: 1985/11/30
memo: めも
```

```tsv
name	kana	birthday
宮崎 あおい	みやざき あおい	1985/11/30
```

## 実現方法

```bash
npm i -S glob
npm i -S js-yaml
npm i -S csv
```

```js
const fs = require('fs');
const glob = require('glob');
const yaml = require('js-yaml');
const stringify = require('csv-stringify/lib/sync')

const files = glob.sync("target/**/profile.yaml", {});
const profiles = files.map(path => {
  const buf = fs.readFileSync(path, 'utf8');
  const obj = yaml.safeLoad(buf);
  return { shimei: obj.shimei, kana: obj.kana, birthday: obj.birthday };
});

const tsvBuf = stringify(profiles, { delimiter: '\t', header: true });
fs.writeFileSync('profiles.tsv', tsvBuf, ()=>{});
```

## cf.

- [CSV Stringifier | Node.js CSV project](http://csv.adaltas.com/stringify/)
