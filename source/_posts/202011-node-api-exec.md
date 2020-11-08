---
title: Node.js - 想要利用 call API 的方式執行伺服器端的檔案，然後將檔案執行結果回傳
date: 2020-11-08 22:49:37
categories:
- w3HexSchool
- node.js
tags:
- w3HexSchool
- node.js
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/590px-Node.js_logo.svg.png
---

Hi，大家好，最近遇到一個需求

有一個 EXE，在執行它之後，它會去更新資料庫的資料

那我想要把這個 EXE 的執行動作包成 API，在客戶端 call 這個 API 時，就去執行 EXE 來進行更新資料

但是，光是這樣子執行 EXE 而已，我就完全無法得知 EXE 執行的結果到底是成功或是失敗，EXE 必須要回應給我更新的結果是成功或是失敗

後來想到可以讓 EXE 回 console 的訊息，然後 API 用執行指令的模組就能夠抓到執行後的回應，這樣就能夠正確的得知 EXE 執行到底有沒有成功的完成更新動作

雖然不確定這樣是不是最穩的方式，不過原先是一直卡不知道要怎麼讓 API 來得到 EXE 的執行結果，一個突然得想到，明明有很多指令是會回應訊息的，如 `ls` 這個用來查詢當前目錄內的情況，會回傳一堆文字，那一定也會有需要擷取這些文字訊息的時候

![](https://i.imgur.com/jCNvGjo.png)

所以最後決定實驗一下，如果讓 EXE 在執行完更新資料庫的動作後，回傳成功或失敗的文字到 console，API 就能夠抓到這個文字來當作回傳結果，進而讓客戶端得知最後結果到底是成功還是失敗

---

## 環境說明

剛剛雖然講了要執行 EXE，不過本篇文要完全以 Node.js 的方式來呈現，蓋一個單純的 .js 執行 console.log，用 express 來開一個最簡易的 get API 來執行它並回應結果

---

## 首先先建一個簡單的 console 回應訊息

#### console.js

```javascript=
console.log('回應一個');
```

---

## 執行指令的模組就用 `child_process` 的 `execFile`

#### exec.js

```javascript=
const { execFile } = require('child_process');

execFile('node', ['console.js'], (error, stdout, stderr) => {
  if (error) {
    console.log('stderr', stderr);
    throw error;
  }

  console.log('stdout', stdout);
});
```

執行結果

![](https://i.imgur.com/eVB9BNr.png)

---

## 再來是 API 的部分，用 express 簡單開個 get

#### api.js

```javascript=
const express = require('express');

const port = 3156;

const app = express();

app.get('/', (req, res) => {
  res.send('123');
});

app.listen(port, () => {
  console.log(`localhost:${port}`)
});
```

---

## 最後把 API 跟 execFile 的使用結合起來，將 stdout 當成 get 的回應

#### exec-api.js

```javascript=
const { execFile } = require('child_process');
const express = require('express');

const port = 3156;

const app = express();

app.get('/', (req, res) => {
  execFile('node', ['console.js'], (error, stdout, stderr) => {
    if (error) {
      console.log('stderr', stderr);
      throw error;
    }

    res.send(stdout);
  });
});

app.listen(port, () => {
  console.log(`localhost:${port}`)
});
```

執行結果

啟動 API 伺服器

![](https://i.imgur.com/ri2YHi7.png)

瀏覽器輸入 API 網址

![](https://i.imgur.com/TYF5zrX.png)

正確的取得檔案執行後的回傳結果了

---

## console.js 的 console 包一層 setTimeout

另外也有實驗用 setTimeout 來模擬非同步的回傳，畢竟操作資料庫實際是需要一些時間的，改動一點點 console.js 的程式碼

```javascript=
setTimeout(() => {
  console.log('回應一個');
}, 1000);
```

這樣瀏覽器在輸入網址後，會轉圈圈個一秒鐘，接著還是能取得到 console.js 的回應結果
