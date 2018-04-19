---
layout: post
title:  "Electronでウィンドウを透明にする"
date:   2018-04-19 22:00:00 +0900
categories: [Node.js, Electron, Vue.js]
image:  "electron-vue-transparent.png"
---

![](/til/static/img/posts/electron-vue-transparent.png)


## やりたいこと

Electron でウィンドウを透明にしたい。
背景だけでなく、タイトルバーや枠も非表示にしてコンテンツのみ表示したい。

実現にあたっての検証は以下の２パターンに対して実施。
公式と Vue のボイラープレート。

- [electron/electron-quick-start](https://github.com/electron/electron-quick-start)
- [SimulatedGREG/electron-vue](https://github.com/SimulatedGREG/electron-vue)


## 実現方法

Electron: __1.8.4__

```js
mainWindow = new BrowserWindow({
  width: 600,
  height: 800,
  transparent: true,
  frame: false,
  toolbar: false
})
```

```css
body {
  -webkit-app-region: drag;
  background: rgba(0, 0, 0, 0);
}
```

`transparent: true` で透明化は達成できるが、この場合はテキストなど
要素の存在するところをクリックしないとウィンドウをアクティブにできない。
これを解決するためには `background: rgba(0, 0, 0, 0);` で完全透明な背景を設定する。

また、タイトルバーを非表示したことによって、ウィンドウの移動ができない。
これは任意の要素に CSS にて `-webkit-app-region: drag;`
を指定すればドラッグが可能になる。
そして Windows においては枠がリサイズのトリガーとなるため、リサイズができない。

### 透過されない？

electron-vue で試していたとき、どうしても透過できなくて小一時間悩んだ。
公式のミニマルなものとの違いを探っていると、最終的に疑わしいのは DevTools
が表示されているかどうかだった。DevTools 右上のオプションから
Dock side をセパレートに変更して問題は解消した。

- [Closing detach devtools disables browser window transparency/vibrancy · Issue #10420 · electron/electron](https://github.com/electron/electron/issues/10420)

electron-vue では、開発モード `npm run dev` でアプリを起動すると自動的に
DevTools を起動する。一度セパレートに変更すれば、その状態を記憶してくれるようだ。


## cf.

- [Frameless Window | Electron](https://electronjs.org/docs/api/frameless-window)
- [Transparent window for Electron app](http://electron.rocks/transparent-window/)
- [Electronでデスクトップいっぱいに雪を降らせるアプリを作る - Qiita](https://qiita.com/nyamogera/items/a2e367b44e78d04ee3a6)
- [Resize transparent window · Issue #6107 · electron/electron](https://github.com/electron/electron/issues/6107)
