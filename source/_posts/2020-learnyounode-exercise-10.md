---
title: learnyounode（Exercise 10 - 報時伺服器）
date: 2020-06-05 23:13:02
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

那麼就開始練習本次的題目吧！這次要解的是第十題的『報時伺服器』

> **TIP**
> 
> 以下內容暴雷了題目的解答，如果是想要先自己嘗試思考並解開題目的人，請斟酌觀看

---

## 挑戰 Exercise 10 - 『報時伺服器』

> **Q：**
> 
> 撰寫一個 TCP 時間伺服器！
> 
> 您的伺服器應該持續堅聽在第一個參數提供的 port 上。每個連線，您都必須以底下的格式回應當前的日期及24小時制的時間：
> 
> "YYYY-MM-DD hh:mm"
> 
> 緊接著是一個 換行（newline） 字元。月、日、小時、時間都必須 填入零到成為2位數。例如：
> 
> "2013-07-06 17:42"

### 劃重點

循著提示來了解幾個重點

* 必須建立一個 TCP 伺服器
* Node 核心的 net 模組
* net 模組有一個名為 net.createServer() 的方法
* net.createServer() 還會回傳一個 server 的實例（instance）。必須呼叫 server.listen(portNumber) 以開始監聽特定的 port
* 第一個參數給 port number
* socket 物件包含一堆和連線有關的 meta-data ，不過這也是一個可讀、可寫的 Node 雙工串流（duplex Stream）。在這個習題中，我們只需要寫入資料，然後關閉 socket
* 使用 socket.write(data) 可以對 socket 寫入資料，以及使用 socket.end() 以關閉 socket 。另外， .end() 方法也可以加上一個 data 物件作為參數，所以您可以很簡單的這樣使用： socket.end(data)

### 程式碼

程式中引入 Node 核心模組 net，使用 net.createServer() 得到的回傳值為 socket，用 socket.end() 達成在連線時回傳一個值

回傳值需要是 "2013-07-06 17:42" 這種日期字串，new Date() 的 getMonth() 記得要 + 1，因為它是從 0 開始算是一月，幾乎都用了 ES6+ 的字串函式 padStart(2, '0') 來將個位數的值補 0

```javascript=
/* exercise10.js */

const net = require('net');

const server = net
  .createServer((socket) => {
    const date = new Date();
    const str = date.getFullYear()
      + '-'
      + String(date.getMonth() + 1).padStart(2, '0')
      + '-'
      + String(date.getDate()).padStart(2, '0')
      + ' '
      + String(date.getHours()).padStart(2, '0')
      + ':'
      + String(date.getMinutes()).padStart(2, '0');

    socket.end(str + '\n');
  })
  .on('error', (err) => {
    throw err;
  });

server.listen(process.argv[2], () => {
  console.log('opened server on', server.address());
});

```

這樣就可以執行 `learnyounode verify exercise10.js` 來通過測驗了

---

## 總結

本篇所挑戰的第十題，很特別是建立了 TCP 伺服器，筆者只懂 Http Request 怎麼發送，就算稍微有聽過三向交握，還是不太了解怎麼對 TCP 伺服器進行溝通 XD，卡了好久才摸索著用提示配不斷的驗證答案來解題 XD
