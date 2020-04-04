---
title: learnyounode (Exercise 6, 7 of 13)
date: 2020-04-04 15:56:21
categories:
- w3HexSchool
- node.js
tags:
- w3HexSchool
- node.js
- learnyounode
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/590px-Node.js_logo.svg.png
---

Hi，大家好，我是小馬彬，清明時節也要來繼續學習 Node.js，筆者挑選的練習素材為 [NODESCHOOL](https://nodeschool.io/zh-tw/) 網站中的 learnyounode 所提供的題目，期望利用它來了解更多關於 Node.js 的基礎應用，如果不知道練習素材的題目是怎麼得來的朋友們，可以到我這系列的第一篇文章 [learnyounode (Exercise 1 of 13)](https://littlehorseboy.github.io/2020/03/13/2020-learnyounode-exercise-1/) 看看

那麼就直接開始練習後續的題目吧

> **TIP**
> 
> 以下內容會直接暴雷題目的解答，如果是想要先自己嘗試思考並解開題目的人，請斟酌觀看

---

## 挑戰 Exercise 6

第六題是『模組化』

> **Q：**
> 
> 這題主要是要練習拆模組，要建立兩個檔案，模組與主程式
> 
> 撰寫一個以副檔名作為篩選條件，列出指定資料夾內檔案的程式
> 
> 主程式的第一個參數是目錄的路徑，第二個參數則是篩選用的副檔名
> 
> 如果第二個參數是「txt」，您將需要篩選出資料夾內 以 .txt 結尾的檔案
> 
> 另外依需求撰寫的模組要提供一個函式，接受三個參數，目錄名稱、附檔名，以及一個 callback 函式
>
> callback 函式必須符合一般的 Node.js 函式的慣例，傳給 callback 函式的第一個參數一定是 error 的內容，沒有 error 發生就會回傳 null，第二個參數回傳的是處理完邏輯後的資料

### 劃重點

循著提示來了解幾個重點

* 新建模組導出的函式接受三個參數，目錄名稱、附檔名，以及一個 callback 函式
* 導出的函式中使用內建核心模組的 fs 對檔案系統進行操作
* 導出的函式中使用內建核心模組的 path 對檔案名稱取得副檔名
* `fs.readdir()` 用來取得一個目錄下所有檔案的檔案名稱
* `path.extname()` 取得副檔名
* 對讀取到的檔案陣列用 `filter()` 篩選出與第二個參數完全符合的檔案
* 主程式中使用函式的第一個參數 process.argv[2] 是目錄的路徑
* 主程式中使用函式的第二個參數 process.argv[3] 是篩選用的副檔名
* 主程式中使用函式的第三個參數是一個函式，會接到 error 內容或是搜尋到的檔案陣列，將檔案陣列 forEach 來 log 出即為解答

### 程式碼

新建一隻 js，筆者這邊將檔案取名為 exercise6.searchExt.js，撰寫一個接收目錄名稱，副檔名，callback 三個參數的函式，函式中用目錄名稱來讀取目錄底下的資料，而後用副檔名篩選出陣列，再用 callback 回傳結果，最後將整個函式用 module.exports 導出

```javascript=
/* exercise6.searchExt.js */

const fs = require('fs');
const path = require('path');

module.exports = (folder, ext, callback) => {
  fs.readdir(folder, (err, files) => {
    if (err) {
      return callback(err);
    }
  
    const filteredFiles = files
      .filter((file) => path.extname(file) === `.${ext}`);
  
    return callback(null, filteredFiles);
  });
};
```

主程式的部分新增了一個 exercise6.js，引入剛剛新建的模組，並將對應的參數塞進新建模組的函式，在 callback 的回傳結果中 log 出所有檔案名稱

```javascript=
/* exercise6.js */

const searchExt = require('./exercise6.searchExt');

searchExt(process.argv[2], process.argv[3], (err, filteredFiles) => {
  if (err) {
    console.error(err);
  }
  filteredFiles.forEach((file) => {
    console.log(file);
  });
});
```

手動輸入指令來看看執行情況

![](https://i.imgur.com/BNexl30.png)

第一個參數為 `.`，指定要搜尋的是當前目錄
第二個參數為 `txt`，指定要篩選的副檔名

筆者指定的目錄底下只有一個 txt 檔，所以輸出 exercise4.txt，輸出正確

這樣就可以執行 `learnyounode verify exercise6.js` 來通過測驗了

---

## 挑戰 Exercise 7

第七題是『HTTP 客戶端』

> **Q：**
> 
> 撰寫一個會對第一個參數所提供之 URL 發出 HTTP GET 請求（request）的程式
> 這個程式會把每個回應「data」事件的字串內容以新的一行 log 到終端機上

### 劃重點

循著提示來了解幾個重點

* 第一個參數 process.argv[2] 是 url 網址，例如 http://google.com 
* 使用 Node.js 內建核心模組的 http.get() 發出請求
* callback 回傳的是 response 物件，它是一個 Node Stream 物件，可以對 Stream 掛上 data、error、end 事件來監聽以取得資料來進行處理
* 掛上 data 的監聽事件回傳的內容為 Buffer 物件，可以先將 Stream 物件 setEncoding('utf8')，直接將回傳內容轉成 string 

### 程式碼

程式中引入 Node 核心模組 http，使用 http.get() 得到的回傳值為 Stream 物件，得再對 Stream 物件掛上 on('data', (chunk) => { }) 進行處理，data 事件回傳值的是 Buffer 物件，也得要特別處理成字串

```javascript=
const http = require('http');

http
  .get(process.argv[2], (res) => {
    res.setEncoding('utf8');
    res.on('data', (chunk) => {
      console.log(chunk);
    });
    res.on('error', (err) => {
      console.error(err);
    });
  })
  .on('error', console.error);
```

手動輸入指令來看看執行情況，網址是故意打錯的，剛好得到回傳內容不會太多

![](https://i.imgur.com/6ABReJV.png)

這樣就可以執行 `learnyounode verify exercise7.js` 來通過測驗了

## 總結

本篇所挑戰的第六題，用到了 CommonJS 的模組約定，應該說一直有在用的 require() 就是 CommonJS 的模組引入方式，這裡是自己客製化了一個模組，利用 module.exports 來導出，導出的是一個函式，不過要知道其實不只能夠導出函式，連純值、物件或陣列都可以當成導出的內容，JS 就是這麼隨興

自定義的模組封裝了使用 fs 及 path 相關的邏輯操作，主程式只要將函式引入，餵進需求的參數，就能得到預期的結果，而不用關心邏輯的程式碼寫了什麼，模組可是在封裝性的程式設計中必定要精熟的技術

本篇所挑戰的第七題，主要是寫後端發送 request 請求的應用，要處理 Stream 物件還要處理 Buffer 物件，算是更加理解一般使用 Node.js 核心模組的操作細節，如果直接使用框架，如 express.js，可能就會少一點點處理這些細節的過程，當然未來一定要往更加省事的方向邁進，但了解更多基底在踩雷時就會有更多的想法可以實驗

終於到有網路應用相關的 Node.js 應用題了，看來接下來就快要能夠看到 Server 的相關應用
