---
title: learnyounode (Exercise 2, 3 of 13)
date: 2020-03-21 09:18:35
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

## 挑戰 Exercise 2

第二題是『第一步』

> **Q：**
> 
> 撰寫一個可以印出命令列參數總和的程式

### 劃重點

循著提示來了解幾個重點

* Node.js 的 global 環境中有 `process` 物件可以直接存取
* process.argv 會接收完整的命令列內的指令
* 假設輸入指令為 `node program.js 1 2 3`，process.argv 的內容為 `['node', '/path/to/your/program.js', '1', '2', '3']`，必定為一個 **Array**
* 從指令的 **第三個參數** 開始才是你要的取用來加總成參數的
* process.argv 的陣列元素皆為 **字串**，需要轉成 **數字** 才能加總

### 程式碼

因為 process.argv 必為陣列，還有真的要用的參數是從第三個開始，所以筆者會先對陣列進行 `slice(2)`，只取陣列中第三個以後的元素

而後用 `reduce((a, b) => a + b, 0)` 將剩餘的參數加總起來，過程中一定要記得將 String 轉成 Number

> **TIP**
> 
> reduce 的第二個參數是用來設置預設值的，如果沒有設定預設值，在陣列沒有任何元素時使用 reduce 是會直接爆炸的，筆者被這雷過不少次 XD

![](https://i.imgur.com/TcsOA1E.png)

那麼以下就是我撰寫提交並且有通過的程式碼

```javascript=
console.log(
  process.argv
    .slice(2)
    .reduce((a, b) => Number(a) + Number(b), 0),
);
```

另外附上官方給的參考解答

![](https://i.imgur.com/U44eBIK.png)

## 挑戰 Exercise 3

第三題是『輸入/輸出』

> **Q：**
> 
> 撰寫一個程式，使用 同步（Synchronous） 操作方式的檔案系統
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

* 要使用 Node.js 內建的 fs 核心模組來進行檔案系統的操作
* 需求要用『同步』方式處理檔案，用 `fs.readFileSync` 而非 ~~`fs.readFile`~~
* 用 `fs.readFileSync` 讀取檔案回返回一個完整檔案內容的 `Buffer` 物件
* Buffer 可以用本身的 toString() 將物件內容轉換成可見字串
* 要讀取換行符號並得知行數可以用 `split('\n')` 切割文字產生陣列，再計算陣列的長度就有可能是正確答案了
* 輸入執行程式的指令時的 **第一個參數** 是檔案路徑
* 測試檔案的最後一行不會以換行符號 \n 結尾，所以最後 Array 元素數目（也就是行數）將會比換行符號多一個。

### 程式碼

先用上一題的檔案內容來測試 `cat 檔案路徑 | wc -l`

![](https://i.imgur.com/08M6iQs.png)

![](https://i.imgur.com/ANt8MeM.png)

輸出是 5，雖然檔案內容的第 6 行有空白，不過並不會計算進去

待會要注意用 split 切割出來的陣列內容是什麼

![](https://i.imgur.com/tj0AVQM.png)

![](https://i.imgur.com/XN7ZC8D.png)

最後要因應劃重點中的最後一項，會將 length - 1

最終有通過的程式碼會是這樣

```javascript=
const fs = require('fs');

const data = fs.readFileSync(process.argv[2]);

console.log(data.toString().split('\n').length);
```

解答參考中提到可以省去使用 toString()，只要把 'utf8' 作為 readFileSync 的第二個輸入參數

改寫過後

```javascript=
const fs = require('fs');

const data = fs.readFileSync(process.argv[2], 'utf8');

console.log(data.split('\n').length - 1);
```

## 總結

雖然才解完了應該算比較不複雜的兩題，不過筆者相信這種主動去思考且動手做看看，定能對這些應用留下更深的印象，將基礎給訓練札實，後續才更加能靈活適應變化

後續的題目不知道會不會越來越難，應該不至於吧 XD
