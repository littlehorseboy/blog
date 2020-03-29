---
title: learnyounode (Exercise 4, 5 of 13)
date: 2020-03-29 12:56:54
categories:
- w3HexSchool
- node.js
tags:
- w3HexSchool
- node.js
- learnyounode
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/590px-Node.js_logo.svg.png
---

Hi，大家好，我是小馬彬，今天要繼續來練習 [NODESCHOOL](https://nodeschool.io/zh-tw/) 的 learnyounode 所提供的題目，如果不知道題目是怎麼得來的朋友們，可以到我的這篇文章 [learnyounode (Exercise 1 of 13)](https://littlehorseboy.github.io/2020/03/13/2020-learnyounode-exercise-1/) 看看

那麼就直接開始練習後續的題目吧

> **TIP**
> 
> 以下內容直接暴雷題目的解答，如果是想要先自己嘗試思考並解開題目的人，請斟酌觀看

---

## 挑戰 Exercise 4

第四題是『非同步輸入/輸出』

> **Q：**
> 
> 撰寫一個程式，使用非同步（Synchronous）的檔案操作系統
 API
> 
> 開啟指定檔案，然後讀取並計算該檔案內容的行(\n)數，最後 console 出計算結果
> 輸出的結果就像是使用下列終端機命令一樣：
>
> ```bash
> cat 檔案路徑 | wc -l
> ```
> 
> 輸入執行程式的指令時的第一個命令列參數將是檔案的完整路徑

### 劃重點

循著提示來了解幾個重點

* 要使用 Node.js 內建核心模組的 fs 來進行檔案系統的操作
* 需求要用『非同步』方式處理檔案，用 `fs.readFile` 而非 ~~`fs.readFileSync`~~
* 用 `fs.readFile` 讀取檔案會返回一個完整檔案內容的 `Buffer` 物件
* Buffer 可以用本身的 toString() 將物件內容轉換成可見字串
* 要讀取換行符號並得知行數可以用 `split('\n')` 切割文字產生陣列，再計算陣列的長度就有可能是正確答案了
* 輸入執行程式的指令時的 **第一個參數** 是檔案路徑
* 測試檔案的最後一行不會以換行符號 \n 結尾，所以最後 Array 元素數目（也就是行數）將會比換行符號多一個。

### 程式碼

因為要讀取檔案，所以先建立一個 txt 檔，並隨意填上幾行文字，然後來執行題目需求的指令看會輸出什麼

![](https://i.imgur.com/gVmKDWp.png)

![](https://i.imgur.com/Yo92CGj.png)

輸出是 3，代表指令計算出這個檔案內容有 3 行，雖然檔案內容的第 4 行有空白，不過並不會計算進去

那麼待會要注意在 js 用 `split('\n')` 切割出來的陣列內容是什麼

![](https://i.imgur.com/xYDRxCH.png)

![](https://i.imgur.com/T2snBLv.png)

可以看到讀取檔案來 `split('\n')` 後得到的陣列長度有 4，多一個是因為陣列內容的最後有一個空白的值，這個是不需要的，所以為了讓測驗通過，需要將 `length - 1`

最終通過的程式碼會是這樣

```javascript=
const fs = require('fs');

fs.readFile(process.argv[2], 'utf8', (err, data) => {
  if (err) {
    return console.error(err);
  }

  console.log(data.split('\n').length - 1);
});
```

---

## 挑戰 Exercise 5

第五題是『篩選』

> **Q：**
> 
> 撰寫一個以副檔名作為篩選條件，列出指定資料夾內檔案的程式
> 
> 程式的第一個參數是目錄的路徑，第二個參數則是篩選用的副檔名
> 
> 如果第二個參數是「txt」，您將需要篩選出資料夾內 以 .txt 結尾的檔案

### 劃重點

循著提示來了解幾個重點

* 第一個參數 process.argv[2] 是目錄的路徑
* 第二個參數 process.argv[3] 是篩選用的副檔名
* 使用 Node.js 內建核心模組的 fs 對檔案系統進行操作
* 使用 Node.js 內建核心模組的 path 對檔案名稱取得副檔名
* `fs.readdir()` 用來取得一個目錄下所有檔案的檔案名稱
* `path.extname()` 取得副檔名
* 對讀取到的檔案陣列用 `filter()` 篩選出與第二個參數完全符合的檔案

### 程式碼

首先要有個目錄結構，筆者以當前的專案目錄來當作要查詢的目標

目前專案目錄下有著副檔名是 .js 以及 .txt 的檔案

![](https://i.imgur.com/ysLC7aq.png)

撰寫後並通過的完整程式碼是這樣

```javascript=
const fs = require('fs');
const path = require('path');

fs.readdir(process.argv[2], (err, files) => {
  if (err) {
    return console.error(err);
  }

  const filteredFiles = files
    .filter((file) => path.extname(file) === `.${process.argv[3]}`);

  filteredFiles.forEach((file) => {
    console.log(file);
  });
});
```

手動執行看看是否有篩選出正確的結果，用 node 指令執行 exercise5.js，並設第一個參數為 `./`，第二個參數為 `txt` 或 `js`

![](https://i.imgur.com/MqmcXnQ.png)

![](https://i.imgur.com/VvyrvVG.png)

## 總結

這一篇所挑戰的題目，加深了對 Node.js 檔案系統模組的運用，讀取檔案會取得 Buffer，簡單轉換過就是可以理解且檢視的檔案內容，以及讀取指定目錄內的所有檔案的檔案名稱，而後利用路徑模組取得副檔名來做篩選，如果要對篩選後的檔案進行內容處理就可以在迴圈內接續的用讀取檔案的模組來撰寫功能

這樣就解完了四題了！第一題 hello world 也算的話那就是五題 XD，連續好幾題都是關於檔案系統的運用，看來檔案操作對後端來說非常的重要呢！
