---
layout: post
title:  "特定の文字列を含むファイルを削除"
date:   2018-03-24 22:00:00 +0900
categories: [Node.js, Windows, Mac]
---

ファイル・フォルダを区別なく削除するには `rimraf` を使う。

- [isaacs/rimraf: A `rm -rf` util for nodejs](https://github.com/isaacs/rimraf)

```js
const fs = require("fs");
const glob = require("glob");
const stringify = require("csv-stringify/lib/sync");
const rimraf = require('rimraf');

module.exports = class FileDealer {
  constructor() {
    this.fileList = [];
  }

  write(path) {
    const quoted = this.fileList.map(path => `"${path}"`);
    const textFile = quoted.join("\r\n") + "\r\n";
    fs.writeFileSync(path, textFile, e => {
      throw new Error(e);
    });
  }

  glob(pattern) {
    const opts = { nocase: true };
    this.fileList = glob.sync(pattern, opts);
  }

  filterByMinSize(minByteSize) {
    const filtered = this.fileList.filter(f => {
      const s = fs.statSync(f);
      return s.size >= minByteSize;
    });
    this.fileList = filtered;
  }

  filterByMaxSize(maxByteSize) {
    const filtered = this.fileList.filter(f => {
      const s = fs.statSync(f);
      return s.size <= maxByteSize;
    });
    this.fileList = filtered;
  }

  outTSV(outPath) {
    const statList = this.fileList.map(f => {
      const s = fs.statSync(f);
      return {
        path: f,
        size: s.size,
        access_time: s.atime,
        modify_time: s.mtime,
        change_time: s.ctime,
        birth_time: s.birthtime
      };
    });
    const file = stringify(statList, { header: true, delimiter: "\t" });
    fs.writeFileSync(outPath, file, e => {
      throw new Error(e);
    });
  }

  delete() {
    this.fileList.forEach(path => {
      console.log(path);
      rimraf.sync(path);
    });
  }
};
```

```js
const FF = require("./FileDealer");

const ff = new FF();
ff.glob("/Photo Library.photoslibrary/Masters/**/*+($ea|@ea)*");
// ff.filterByMaxSize(100);
ff.write("result.txt");
// ff.outTSV("result.tsv");
ff.delete();
```

```js
  "dependencies": {
    "csv-stringify": "^2.0.4",
    "glob": "^7.1.2",
    "rimraf": "^2.6.2"
  }
```
