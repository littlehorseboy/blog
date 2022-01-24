---
title: Nuxt.js 利用 serverMiddleware 讓 page.vue 接 POST 來的資料
date: 2020-01-15 21:00:50
categories:
- nuxt
- serverMiddleware
tags:
- nuxt
- serverMiddleware
cover: https://nuxtjs.org/design-kit/colored-logo.svg
---

Hi，大家好，我是小馬彬，最近在公司都在玩 Nuxt.js，其實得說大部分的需求在官方文檔上都能找到解方，主要要區分好組件生命週期是後端渲染的部分還是前端渲染的部分，不過這次會寫這篇的主要目的就是因為標題上的敘述

> 需求是想要讓某個 page 能夠接到從其他頁面的表單 POST 送出跳轉過來的資料

---

## 2020/1/16 修正 2020/1/15 所述內容

其實可以直接用 Nuxt 提供的 context 來得到 POST 來的資料，根本不必特別玩 serverMiddleware 啦，不過就當補充知識吧！

直接在 pages/index.vue 即可

```javascript
asyncData({ req }) {
  console.log(req.body);
},
```

---

## 以下為 2020/1/15 撰寫

起初還以為可以直接利用 Nuxt 提供的 context 來得到 POST 來的資料

![](https://i.imgur.com/oasAHFN.png)

但是

**想得太美了！**

把這幾個看起來比較有可能跟接 POST 資料有關的 req, res 都 console 出來，結果是偏尋不著，原來還是得特別跟 node.js 打打交道

後來 google 到 serverMiddleware 這個解方，它可以將一隻類似 node.js http.createServer 的 function 掛給某一個 path 來使用

---

## serverMiddleware

筆者當初創建 Nuxt 專案時沒有使用 express，所以以下紀錄的方式是一般的 node.js 使用方式

首先

```bash
npm i querystring
```

要利用 querystring 這個第三方函式庫來 parse 其他頁面所 POST 來的資料

創建一隻 js

~/middleware/postRequestHandler.js

```javascript
const querystring = require('querystring');

export default (req, res, next) => {
  let body = '';

  req.on('data', (data) => {
    body += data;
  });

  req.on('end', () => {
    req.body = querystring.parse(body) || {};
    next();
  });
};
```

這段程式碼做的事情是讓接到 request 的時候，將 data 給塞進 req.body 中，body 的部分得依靠第三方函式 querystring.parse 過才能正常，就像使用 express 時也會用到 bodyParser.json() 來將 POST 資料 parse 過

然後要特別的到 nuxt.config.js 來設定 serverMiddleware

```javascript
serverMiddleware: [
  {
    path: '/', handler: '~/middleware/postRequestHandler',
  },
],
```

這樣在 pages/index.vue 中就能直接利用 asyncData 來取得 req.body 的內容

```javascript
asyncData({ req }) {
  console.log(req.body.abc);
},
```

這邊接 abc 是因為筆者用 Postman 測試發 request 的測試資料就是 abc: 123 到我的 `/` path

這樣就能正常取得 POST 來的資料了

---

回到 nuxt.config.js 的 serverMiddleware 的配置，似乎一個 path 只能配一個 handler，除了一些特殊的第三方函式庫，因為筆者有試著將不同 path 接同一個 handler 或是將 path 改用正規表示法來寫，似乎都只有第一個 path 會生效，而且還可能會讓第二個 path 直接當掉，這部分算是還不夠熟悉 Nuxt 的底層，其實也不打算熟悉啦，它蠻多架構的用法有搞清楚是真的省了些麻煩的，不過 google 了一下也沒看到有人用多個 path 給同一隻 handler 用，就當作它只能一個配一個吧！
