---
title: VSCode - 做一個屬於你自己的擴充套件（Extension） - Hello World
date: 2020-10-11 17:28:40
categories:
- w3HexSchool
- VSCode
- Extension
tags:
- w3HexSchool
- VSCode
- Extension
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/9/9a/Visual_Studio_Code_1.35_icon.svg/512px-Visual_Studio_Code_1.35_icon.svg.png
---

Hi，大家好，最近心血來潮的想做一個程式碼格式化的工具，要針對 SQL 來做處理，格式化的需求想要能完全按照自己的特殊習慣來調整

原本打算直接用個 HTML 的 textarea 直接寫邏輯來做，但是，沒有那麼簡單啊！比想像中的還要有料，不是，是比想像中的還要困難 XD，在一陣東查查西找找後，覺得這要完全認得一堆 SQL 的關鍵字或什麼小括號、大括號等等的，如果冒冒失失的做成一遇到括號就亂給空白那絕對不行

後來不知怎的想到可以玩看看還沒有玩過的 VSCode 擴充套件，希望可以藉由練習玩 VSCode 擴充套件，來更了解 IDE 的功能，進而發掘程式碼格式化的眉角

---

## [Your First Extension](https://code.visualstudio.com/api/get-started/your-first-extension)

### 安裝擴充套件專案生成懶人包

```bash
npm i -g yo generator-code
```

---

### 執行擴充套件專案生成

```bash
npm i -g yo generator-code
```

### 選擇 New Extension (TypeScript)

![](https://i.imgur.com/b5AzdFu.png)

### 輸入專案名稱 (name)，然後就一路 Enter 到底

![](https://i.imgur.com/SJri9jV.png)

---

### 這時會生成一個 hello-world 的專案資料夾

專案結構是這個樣子的

![](https://i.imgur.com/MA6Ubuh.png)

---

### 介紹一下 package.json 的重點部分

![](https://i.imgur.com/qboV2Tl.png)

#### activationEvents

這個屬性設定執行的事件

onCommand 代表的是 VSCode 執行指令時觸發

#### main

執行的主程式路徑

#### contributes

VSCode 執行的指令，Ctrl + Shift + P 然後輸入 title 的內容 Hello World 就可以執行主程式

---

### src/extensions.ts 預設的程式碼

![](https://i.imgur.com/sagfJQn.png)

16 行 註冊了指令 hello-world.helloWorld

20 行 執行 show message

---

### 運行起來，F5

![](https://i.imgur.com/YgWkL6u.png)

開啟了新的編輯器視窗，視窗上的文字有 [延伸模組開發主機]

### Ctrl + Shift + P，然後輸入指令

![](https://i.imgur.com/O28U9hS.gif)

---

## 總結

以上就是自己建一個 VSCode Extension 的基本方式，還有很多 vscode 的屬性還沒介紹到，以及最後一定要發布 Extension，留待之後有機會會再繼續補

---

## 參考來源

https://code.visualstudio.com/api/get-started/your-first-extension
