---
title: learnyounode（Exercise 1 - HELLO WORLD）
date: 2020-03-13 22:16:45
categories:
- w3HexSchool
- node.js
tags:
- w3HexSchool
- node.js
- learnyounode
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Node.js_logo.svg/590px-Node.js_logo.svg.png
---

Hi，大家好，我是小馬彬，最近想要把 Node.js 的應用知識給補起來，本來想要買一本中文書，不過查了查博X來發現好像沒有喜歡的，應該說真的用了關鍵字查到想買的是 2016 年出版的 XD，不過因為版本更迭應該差蠻多的，毅然決然地開始尋找其他資源，最後就在 Node.js 的官方網站上發現這個

## NODESCHOOL

https://nodeschool.io/zh-tw/

![](https://i.imgur.com/9rDGOSy.png)

居然還有中文呢，絕對不是因為筆者英文不行，只是怕看英文會吸收得太慢而且不良於學（不就是英文不行嗎 XD）

## 安裝說明

在瀏覽過後看起來資源非常豐富，有各種不同的主題的挑戰

![](https://i.imgur.com/knqX4gN.png)

就先來裝個 Node.js 的基礎來試手看看了

`npm install -g learnyounode`

安裝完成後就能直接輸入指令 `learnyounode` 來開啟挑戰

筆者是用 Windows 10 家用版來開啟，最一開始執行 `learnyounode` 的時候顯示了現有執行策略不允許執行的說明

![](https://i.imgur.com/aklw3pr.png)

learnyounode 是 .ps1 檔，筆者爬文後知道得設定了 `Set-ExecutionPolicy RemoteSigned`（使用此設置需要以系統管理員身分）後就可以正常執行程式，更詳細的設定參數可以參考 [官方 Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7)

## 執行及切換 Language

那麼再次輸入 `learnyounode`，就會看到如下的畫面

![](https://i.imgur.com/TYShS3W.png)

已經偷跑了三題 XD，不過這篇文會只講第一題

先改成中文才方便閱讀題目（覺得很重要，但或許只有筆者會需要 XD）

選擇 `CHOOSE LANGUAGE` 之後選擇 `Taiwanese (繁體中文)` 得到全繁體中文啦～～

![](https://i.imgur.com/GRisEzb.png)

接下來因為操作這個介面的步驟並不難，就不會每個步驟都將畫面上的內容給完整截圖下來貼上

## 挑戰開始 Exercise 1

第一題要做的是輸出 『HELLO WORLD』 到終端機，沒錯！一開始總是要 HELLO WORLD 一下

開啟一隻 js 來撰寫程式碼

![](https://i.imgur.com/OvHsb2C.png)

就這樣，依照他提供的指令對你寫出來的檔案進行操作

![](https://i.imgur.com/LgxvKtG.png)

就能看到通過的結果了

![](https://i.imgur.com/0IzgFV5.png)

## 總結

筆者覺得這個資源是適合我的，或許也適合大家，剛好在尋找學習 Node.js 的資源時發現的，算是小小推坑文，後續幾篇文會陸續將其他題解題的過程給紀錄下來
