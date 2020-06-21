---
title: learnyounode（Exercise 12 - HTTP 回傳大寫字體）
date: 2020-06-21 23:04:51
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

那麼就開始練習本次的題目吧！這次要解的是第十二題的『HTTP 回傳大寫字體』

> **TIP**
> 
> 以下內容暴雷了題目的解答，如果是想要先自己嘗試思考並解開題目的人，請斟酌觀看

---

## 挑戰 Exercise 12 - 『HTTP 回傳大寫字體』

> **Q：**
> 
> 撰寫一個只能接收 POST 請求的 HTTP 伺服器，這個伺服器會把收到的 POST 內容字元轉換成大寫，並回應給客戶端。
> 
> 您的伺服器應該監聽在第一個參數所給予的 port 上。

### 劃重點

循著提示來了解幾個重點

* 必須建立一個 HTTP 伺服器
* 接收 POST 請求
* 回傳大寫文字

### 程式碼

```javascript=
/* exercise12.js */

const http = require('http');

const server = http
  .createServer((req, res) => {
    let body = '';
    req.on('data', (chunk) => {
      if (req.method !== 'POST') {
        throw new Error('error');
      }
      body += chunk;
    });
    req.on('end', () => {
      res.writeHead(200, { 'content-type': 'application/json' });
      res.write(body.toUpperCase());
      res.end();
    });
  })
  .on('error', (err) => {
    throw err;
  });

server.listen(process.argv[2], () => {
  console.log('opened server on', server.address());
});
```

這樣就可以執行 `learnyounode verify exercise12.js` 來通過測驗了

---

## 總結

本篇挑戰的第十二題，感覺算是比較簡單的建立伺服器 XD，只要記得判斷 req.method 等於 POST 及回傳內容要轉大寫即可
