---
title: learnyounode（Exercise 11 - HTTP 檔案伺服器）
date: 2020-06-14 22:22:39
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

那麼就開始練習本次的題目吧！這次要解的是第十一題的『HTTP 檔案伺服器』

> **TIP**
> 
> 以下內容暴雷了題目的解答，如果是想要先自己嘗試思考並解開題目的人，請斟酌觀看

---

## 挑戰 Exercise 11 - 『HTTP 檔案伺服器』

> **Q：**
> 
> 撰寫一個 HTTP 伺服器 ，可以提供它收到的 text 檔案給所有收到的請求。
> 
> 第一個參數是 port ，您的伺服器應該監聽在第一個參數所給予的 port 上。
> 
> 第二個參數是提供的檔案。必須使用 `fs.createReadStream()` 方法將檔案內容傳遞到回應上。

### 劃重點

循著提示來了解幾個重點

* 必須建立一個 HTTP 伺服器
* fs 核心模組也有一些針對檔案的串流 API 可以用。將會需要用 fs.createReadStream()
* fs.createReadStream() 將第一個參數指定的檔案建立成串流
* .pipe(res) 把檔案系統串流和 HTTP 回應串流連在一起

### 程式碼

```javascript=
/* exercise11.js */

const http = require('http');
const fs = require('fs');

const server = http
  .createServer((req, res) => {
    res.writeHead(200, { 'content-type': 'text/plain' });

    const readStream = fs.createReadStream(process.argv[3]);

    readStream
      .on('open', () => {
        readStream.pipe(res);
      })
      .on('error', (err) => {
        res.end(err);
      });
  })
  .on('error', (err) => {
    throw err;
  });

server.listen(process.argv[2], () => {
  console.log('opened server on', server.address());
});

```

這樣就可以執行 `learnyounode verify exercise11.js` 來通過測驗了

---

## 總結

本篇所挑戰的第十一題，fs.readFile 也能達到一樣的事情，達到讀取檔案並回傳，但如果讀取的檔案有 200MB，那使用 readFile 可能會瞬間占掉 200MB 的內存來放這個檔案

fs.createReadStream() 不同的是，它用串流的方式來讀取檔案，切成一塊一塊 chunk(大多是 16k) 傳給發送請求的地方，在讀取到大容量的文件時，能有效控制 I/O 開銷
