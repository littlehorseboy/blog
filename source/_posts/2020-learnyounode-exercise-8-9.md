---
title: learnyounode（Exercise 8 - HTTP 資料收集，Exercise 9 - 玩轉非同步）
date: 2020-05-24 21:05:04
categories:
- w3HexSchool
- node.js
tags:
- w3HexSchool
- node.js
- learnyounode
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/590px-Node.js_logo.svg.png
---

Hi，大家好，今天要來解 Node.js 的練習題，題目來源為 [NODESCHOOL](https://nodeschool.io/zh-tw/) 的 [learnyounode](https://github.com/workshopper/learnyounode)，期望透過解題來了解更多關於 Node.js 的基礎應用知識

如果還不知道題目是怎麼得來的，或是想知道更詳細的 learnyounode 使用教學，可以到我這個系列的第一篇文章 [learnyounode (Exercise 1 of 13)](https://littlehorseboy.github.io/2020/03/13/2020-learnyounode-exercise-1/) 看看

那麼就開始練習本次的題目吧！這次要解的是第八題的『HTTP 資料收集』以及第九題的『玩轉非同步』

> **TIP**
> 
> 以下內容暴雷了題目的解答，如果是想要先自己嘗試思考並解開題目的人，請斟酌觀看

---

## 挑戰 Exercise 8 - 『HTTP 資料收集』

> **Q：**
> 撰寫一個會對第一個參數所提供之 URL 發出 HTTP GET 請求（request）的程式
> 收集每個「data」事件回傳的字串內容，最後 console.log 出來
> 需求要 log 出兩行，一是**字串的字數**，二是**完整字串內容**

### 劃重點

循著提示來了解幾個重點

* 第一個參數 process.argv[2] 是 url 網址，例如 http://google.com 
* 使用 Node.js 內建核心模組的 http.get() 發出請求
* callback 回傳的是 response 物件，它是一個 Node Stream 物件，可以對 Stream 掛上 data、error、end 事件來監聽以取得資料來進行處理
* 掛上 data 的監聽事件回傳的內容為 Buffer 物件，可以先將 Stream 物件 setEncoding('utf8')，直接將回傳內容轉成 string
* 設置一個變數將 data 事件的回傳值 += 存入
* 在 end 事件做最終的 console.log 處理 

### 程式碼

程式中引入 Node 核心模組 http，使用 http.get() 得到的回傳值為 Stream 物件，得再對 Stream 物件掛上 on('data', (chunk) => { }) 進行處理，data 事件回傳值的是 Buffer 物件，也得要特別處理成字串

增加一個變數 result，接下 data 事件回傳的 chunk 內容，最後在 end 事件輸出

```javascript=
/* exercise8.js */

const http = require('http');

http
  .get(process.argv[2], (res) => {
    let data = '';
    res.setEncoding('utf8');
    res.on('data', (chunk) => {
      data += chunk;
    });
    res.on('end', () => {
      console.log(data.length);
      console.log(data);
    });
    res.on('error', (err) => {
      console.error(err);
    });
  })
  .on('error', console.error);
```

手動輸入指令來看看執行情況，網址是故意打錯的，剛好得到回傳內容不會太多，不然截圖截不下 XD

![](https://i.imgur.com/nSYDR7y.png)

這樣就可以執行 `learnyounode verify exercise8.js` 來通過測驗了

---

## 挑戰 Exercise 9 - 『玩轉非同步』

> **Q：**
> 與前一題類似，但會有三個命令列參數取得 URL 來發出 HTTP GET 請求（request），完整收集三個網址的內容後才輸出，必須按照參數的 URL 順序來顯示對應的內容

### 劃重點

循著提示來了解幾個重點

* http.get() 在非同步處理完畢後是觸發 end 事件
* 要在三個 http.get() 的 end 非同步事件回傳後進行輸出

### 程式碼

定義一個接受 url 參數而後回傳 Promise 的 function，function 內使用 http.get()，在 end 事件用 resolve 回傳 result

這是有三個 url 參數，就執行三次剛剛定義的 function，傳入不同 url，得到各自不同 get 目標的 Promise

最後用 Promise.all 將有各自不同目標的 Promise 包成陣列來執行，就能得到非同步回傳且會照順序排列的結果

```javascript=
/* exercise9.js */

const http = require('http');

function asyncGet(url) {
  return new Promise((resolve, reject) => {
    http
      .get(url, (res) => {
        let result = '';
        res.setEncoding('utf8');
        res.on('data', (chunk) => {
          result += chunk;
        });
        res.on('end', () => {
          resolve(result);
        });
        res.on('error', (err) => {
          reject(err);
        });
      })
      .on('error', reject);
  });
}

Promise.all([
  asyncGet(process.argv[2]),
  asyncGet(process.argv[3]),
  asyncGet(process.argv[4]),
])
  .then((values) => {
    values.forEach((value) => {
      console.log(value);
    });
  })
  .catch((error) => {
    console.error(error);
  });

```

手動輸入指令來看看執行情況

![](https://i.imgur.com/qrwA2om.png)

這樣就可以執行 `learnyounode verify exercise9.js` 來通過測驗了

## 總結

本篇所挑戰的第八題，主要是寫後端發送 request 請求的應用，要處理 Stream 物件還要處理 Buffer 物件，算是更加理解一般使用 Node.js 核心模組的操作細節，如果直接使用框架，如 express.js，可能就會少一點點處理這些細節的過程，當然未來勢必要用更加省事的方式，不過在了解更多 Node.js 基底就有機會在踩雷時有更多的觀念可以解決發生的問題

第九題則是處理多筆非同步 function 回傳的技巧，筆者自己是習慣用 Promise.all 來處理，如果要額外裝好用的函式庫處理非同步，RxJS 是個不錯的選擇
