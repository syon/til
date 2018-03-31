---
layout: post
title:  "[Node.js]シェルコマンド mkdir -p でフォルダを再帰的に作成"
date:   2018-03-31 13:00:00 +0900
categories: [Mac, Shell]
---

## やりたいこと

```bash
$ mkdir -p path/to/nested/dir
```

## 実現方法

- [ShellJS](https://documentup.com/shelljs/shelljs)
- https://github.com/shelljs/shelljs

Node.js上でこう書ける。

```js
shell.mkdir("-p", "path/to/nested/dir");
```

## Installing

```bash
npm i -S shelljs
```

## Examples

基本的なコマンドは関数としてサポートされており、外部コマンドは`shell.exec`で呼び出すことができる。

```js
var shell = require('shelljs');

if (!shell.which('git')) {
  shell.echo('Sorry, this script requires git');
  shell.exit(1);
}

// Copy files to release dir
shell.rm('-rf', 'out/Release');
shell.cp('-R', 'stuff/', 'out/Release');

// Replace macros in each .js file
shell.cd('lib');
shell.ls('*.js').forEach(function (file) {
  shell.sed('-i', 'BUILD_VERSION', 'v0.1.2', file);
  shell.sed('-i', /^.*REMOVE_THIS_LINE.*$/, '', file);
  shell.sed('-i', /.*REPLACE_LINE_WITH_MACRO.*\n/, shell.cat('macro.js'), file);
});
shell.cd('..');

// Run external tool synchronously
if (shell.exec('git commit -am "Auto-commit"').code !== 0) {
  shell.echo('Error: Git commit failed');
  shell.exit(1);
}
```
