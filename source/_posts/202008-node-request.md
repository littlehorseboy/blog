---
title: Node.js - 該如何用 Node.js 核心模組發送 request 呢？
date: 2020-08-02 12:17:51
categories:
- w3HexSchool
- node.js
tags:
- w3HexSchool
- node.js
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/590px-Node.js_logo.svg.png
---

Hi，大家好，這篇想要紀錄如何用 Node.js 的核心模組 http 來發送 request，http 模組有個 property 是名為 request 的 function，`http.request()`，可以用它來發送請求，並從其它網站取得需要的各種資料

> TIP：
> 另外還有 http.get() 也可以達到與 http.request() 差不多的需求，但是，就沒有提供 http.post，所以筆者就直接選擇都用 http.request()，只要在 options 定義 method 是 `GET` 或是 `POST` 等等，感覺比較能方便寫個重複使用，僅變換參數的 function

---

## 環境說明

只需要有安裝 Node.js，http 模組是 Node.js 中標配的模組，可以直接導入來做使用，不需要從 npm 下載其它依賴的第三方

---

## 以前端的角度來看

前端來看發送請求這件事，應該都會用習慣了像 jQuery 的 $.ajax，亦或是 axios 這種第三方函式庫，它們已經很成熟的封裝掉很多繁瑣的程式，而且使用方式簡便，還有不少延伸功能可以直接做複雜的 ajax 需求，所以可能會覺得在使用 Node.js 的 http.request() 時稍微不那麼 friendly，才促使了想要寫這篇記錄

那接下來就先請出本篇文章主要的示範程式碼

---

## 從 github API 取得某個使用者資料的範例

目標要取得資料的網址為

https://api.github.com/users/littlehorseboy

以下為用 `GET` 請求該網址的完整程式碼

```javascript=
const { request } = require('https');

const options = {
  hostname: 'api.github.com',
  port: 443,
  path: '/users/littlehorseboy',
  method: 'get',
  headers: {
    'User-Agent': 'littlehorseboy',
  },
};

const req = request(options, (res) => {
  console.log(`STATUS:  ${res.statusCode}`);
  console.log(`HEADERS: ${JSON.stringify(res.headers)}`);

  res.setEncoding('utf8');

  let data = '';

  res.on('data', (chunk) => data += chunk);

  res.on('end', () => console.log(data));
});

req.on('error', (error) => console.error(error));

req.end();
```

## 程式碼逐行說明

**第 1 行** `require('https')`

```javascript
const { request } = require('https');
```

因為 github API 的網址是 https 開頭，所以這段程式用 https 的 request，如果是向 http 開頭的網址發送請求，就可以改用 http 的 request，主要是 http 是無法正常取得 https 網站的資料的，都會拿到被拒絕的回傳資料，算是個小小需要注意的地方

**第 3 ~ 11 行** 定義描述指定資源的溝通方式

```javascript
const options = {
  hostname: 'api.github.com',
  port: 443,
  path: '/users/littlehorseboy',
  method: 'get',
  headers: {
    'User-Agent': 'littlehorseboy',
  },
};
```

用來決定你的請求目標，http method 及 headers，options 是準備要放在 request() 函式的第一個參數中

`http.request(options[, callback])`

此例中在 headers 寫 User-Agent 並不是必須的，這是要玩 github API 必須加上，github API 文檔有說明

還有一點要特別說明，上面的程式碼只有說明 GET 請求的寫法，如果是要發送 POST 請求，通常都會多寫入 post data，但是 post data 並不是直接加在 options 內，正確的寫入方式會在文章下方的 **第 28 行** 說明

**第 13 ~ 24 行** 發送請求接到 response 後的處理，此時還未進行發送

```javascript
const req = request(options, (res) => {
  console.log(`STATUS:  ${res.statusCode}`);
  console.log(`HEADERS: ${JSON.stringify(res.headers)}`);

  res.setEncoding('utf8');

  let data = '';

  res.on('data', (chunk) => data += chunk);

  res.on('end', () => console.log(data));
});
```

這段程式與串流模組的 Stream 用法非常類似，`res.on('data', (chunk) => data += chunk)` 逐一接收封包，直到 `res.on('end', () => console.log(data))` 回應方傳送停止時，就執行最後的動作

真正的 **發送** 動作在文章下方的 **第 28 行** 說明 

**第 26 行** error 處理

```javascript
req.on('error', (error) => console.error(error));
```

**第 28 行** `req.end()`，http.request() 一定要在使用 req.end() 後才會 **發送**

```javascript
req.end();
```

一定要在使用 req.end() 後才會 **發送**，然後跑進 **第 13 ~ 24 行** 接到 res 進行後續指令

另外要補充寫入 post data 的方式，得用到 req.write()，以下為將 **第 28 行** 程式碼改寫的範例

```javascript
const { request } = require('https');
const querystring = require('querystring');

// 略 ...

const postData = querystring.stringify({
  msg: 'Hello World!',
});

req.write(postData);
req.end();
```

或是

```javascript
const { request } = require('https');
const querystring = require('querystring');

// 略 ...

const postData = querystring.stringify({
  msg: 'Hello World!',
});

req.end(postData);
```

> TIP:
> 原來 Node.js 的核心模組標配中就有 querystring
> 如果是前端還有用過 axios，可能就有曾經下載過 npm 上的第三方 querystring 函式庫
> 差點以為在後端沒有這模組也要去下載

---

## 總結

以上就是對 Node.js 核心模組 request 的不專業分析

其實大可以直接 `npm i axios` 來替代 `http.request` 使用，不過出於好奇心又想都通通清楚，就栽下去多看了一些官方文件說明來研究看看
