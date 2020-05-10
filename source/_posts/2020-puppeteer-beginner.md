---
title: Puppeteer，簡單易用的 Node.js 爬蟲
date: 2020-05-10 22:27:49
categories:
- w3HexSchool
- Puppeteer
tags:
- w3HexSchool
- Puppeteer
cover: https://user-images.githubusercontent.com/10379601/29446482-04f7036a-841f-11e7-9872-91d1fc2ea683.png
---

Hi，大家好，最近想要用爬蟲來做一些重複的網頁操作動作，所以就來玩玩看 Google 的 [Puppeteer](https://pptr.dev/)，預期要做的動作是進入網頁，輸入帳密登入，然後操作表單，最後送出表單

---

## [Puppeteer](https://pptr.dev/)

Puppeteer 是 Google 出的 Node.js 爬蟲，用來操作 Chrome 或是 Chromium，Firefox 也有，不過[依官方說法應該是不太推](https://github.com/puppeteer/puppeteer/tree/master/experimental/puppeteer-firefox)

### 建立一個 puppeteer 專案

```bash
$ npm i -D puppeteer
```

下載過程中，他會順便幫你下載 Chromium 到 node_modules\puppeteer 底下，然後就可以開一隻 js 檔，直接撰寫爬蟲的腳本了

### 爬蟲腳本

筆者在根目錄建立一個 google.js，裡面要引用 puppeteer 來實作輸入 input，而後按下鍵盤 Enter 的動作

示範的網頁就是 Google 的搜尋首頁啦～

https://google.com/

![](https://i.imgur.com/UBH71Si.png)

這邊我們要先取得用來輸入搜尋關鍵字的 input 的 selector 語法

![](https://i.imgur.com/mZQh255.png)

### 取得 selector 語法的小技巧

因為要取得 HTML 元素，要先能夠挖出他 HTML 的層級關係或是 id、class 來拼出 selector 語法，不過其實 DevTools 有很方便的功能，大多數的瀏覽器都有這功能

#### 滑鼠右鍵 -> 檢查

![](https://i.imgur.com/sY0L6XA.png)

#### 滑鼠右鍵 -> Copy -> Copy selector

![](https://i.imgur.com/w0va8vU.png)

就 copy 了 selector 的字串，直接在程式碼中貼上使用即可

### 回到 google.js 進行撰寫

以下就是完整的腳本

```javascript=
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({
    headless: false, // 不使用 headless 模式，就會開啟瀏覽器來實際動作
    slowMo: 150, // 每個動作的間隔時間，方便觀察實際動作
  });
  const page = await browser.newPage(); // 開啟新分頁

  await page.goto('https://google.com');

  const input = await page.$('#tsf > div:nth-child(2) > div.A8SBwf > div.RNNXgb > div > div.a4bIc > input');

  if (input) {
    await input.focus();
    await input.type('horse');
  }

  await page.keyboard.press('Escape');

  await page.keyboard.press('Enter');

  // browser.close();
})();
```

### 用 node 指令執行腳本

直接輸入 node google.js 就可以看到成果啦！～

![](https://i.imgur.com/LhJp7l3.gif)

## 總結

是不是很簡單呢？如果有一些要用網頁操作的重複動作或大量的動作，就能寫好腳本，輕鬆執行啦～，除了本篇文中用到的表單動作、鍵盤輸入以外，Puppeteer 還有提供易用的網頁截圖功能、擷取成 PDF 的功能，另外就是你可能想得到的，到不同網站撈取大量資料或是圖片？

打算在之後要用 Puppeteer 來實作 E2E Testing，筆者是認為，E2E Testing 實際上就是 `爬蟲 + 測試函式庫` 的組合技，所以...敬請期待囉～ XD
