---
title: Javascript 的 Boolean 居然能這樣用（搭配 Array）
date: 2020-01-24 21:28:19
categories:
- javascript
tags:
- javascript
---

Hi，大家新年快樂，我是小馬彬，趁著除夕夜深人靜時來把最近發現的特別 JS 用法整理一下，Javascript 的 Boolean 居然能夠直接用在 Array.filter() 內的函式

## filter

```javascript
['1', '', '3'].filter(Boolean); // ['1', '3']
// 等同於
['1', '', '3'].filter((item) => item); // ['1', '3']
// 亦或是
['1', '', '3'].filter((item) => Boolean(item)); // ['1', '3']
```

![](https://i.imgur.com/Wg0qt7l.png)

這樣就能少打一點點字，而且好像在程式碼閱讀上也算更直觀（前提是理解可以這樣用的情況下，不然一開始我看到的時候真的黑人問號），用來排除 `false, null, undefined, 0, NaN, ''`

![](https://i.imgur.com/ztcjBAV.png)

順便好奇其他常用的 Array 函式在遇到 Boolean 會發生什麼事

## map

遇到 map 就會轉成 true 或 false

![](https://i.imgur.com/SWJQcuV.png)

## find

遇到 find 就會取得第一個比對後產生 true 的值

![](https://i.imgur.com/RNTbcjY.png)

## findIndex

遇到 findIndex 就會取得第一個比對後產生 true 的 index

![](https://i.imgur.com/JUR615T.png)

## every

every 比對陣列內要全為 true，否則為 false

![](https://i.imgur.com/Z1A4SMg.png)

## some

some 只要一個值為 true，最終就會回傳 true

![](https://i.imgur.com/0ZaPT0Z.png)

大略拿了一些可能會真實用得上的 Array 函式來寫

---

以上就是發現 Boolean 居然能這樣用 filter，檢驗過後覺得似乎真的可以當不錯的簡寫方式，寫這篇時又突然一個想到還能夠搭配不同的 Array 函式，如果你很常用預設的寫法來判斷 `false, null, undefined, 0, NaN, ‘’`，那可能就可以考慮用這個 Boolean 來縮短一點點程式碼
