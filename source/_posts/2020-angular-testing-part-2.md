---
title: Angular Testing（Part 2）- 環境說明
date: 2020-04-19 22:21:56
categories:
- w3HexSchool
- Angular
tags:
- w3HexSchool
- Angular
- Unit Test
cover: https://angular.tw/assets/images/logos/angular/angular.svg
---

Hi，大家好，上一篇文章簡單介紹了 Angular CLI 一手裝好裝滿的單元測試環境，分別淺淺的講了 Jasmine 及 Karma，還有如何啟動測試，今天要先在更往 CLI 專案裡面看一看究竟放了什麼葫蘆什麼藥

## angular.json

首先就從根目錄位置的 `angular.json` 開始看起，ng new 產生一個新專案後，打開 `angular.json` 應該會在 83 行的位置看到，有個 `"test"` 的設置

![](https://i.imgur.com/MiZ0Rig.png)

`"test"` 內的設置是測試時的建構選項預設值，並為 ng test 命令提供額外的預設值以供執行測試，其中特別要拿來說明的是 `"main": "src/test.ts"` 及 `"karmaConfig": "karma.conf.js"`，他們就是 Angular CLI 的單元測試設置檔

## src/test.ts

![](https://i.imgur.com/pZqFEio.png)

test.ts 的 getTestBed() 是用來動態建立一個模擬 @NgModule 的 Angular 測試模組，看起來這邊用 getTestBed() 打造出類似 app.module.ts 的基底

23 行 `require.context('./', true, /\.spec\.ts$/);` 再遞迴你整個專案的 xxx.spec.ts 來進行測試

## karma.conf.js

![](https://i.imgur.com/Z671Vtm.png)

karma.conf.js 寫著要整合的測試框架 jasmine、angular 相關的環境、測試覆蓋率產生配置，以及測試時使用的瀏覽器等等

## ng generate service 來觀察 service.spec.ts

先 ng new 一個乾淨的專案

然後在終端機輸入 `ng generate service test`

![](https://i.imgur.com/8dMMmv3.png)

就會產生 test.service.ts 以及 test.service.spec.ts

![](https://i.imgur.com/WE40RC5.png)

![](https://i.imgur.com/kl8vqxM.png)

這時就直接執行 `npm run test` 就會得到測試的結果

![](https://i.imgur.com/bBKPQhK.png)

app.component.spec.ts 的程式碼目前都被我刪掉了，現在先專注在這一個 service 的檔案上

### configureTestingModule 定義

傳給 configureTestingModule 的模組定義是 @NgModule 元資料屬性的子集

為了說明這點，筆者要將 test.service.ts 的 meta 中定義的 `providerIn: 'root'` 給刪掉

![](https://i.imgur.com/bbtZTfH.png)

這時就噴錯啦

![](https://i.imgur.com/tIEU2wP.png)

TestBed 無法正常的 inject 進 TestService，需要對 configureTestingModule 設定 providers 才行

![](https://i.imgur.com/hki3JFV.png)

![](https://i.imgur.com/sObwloZ.png)

所以 TestBed.configureTestingModule() 就類似定義了 @NgModule 這種配置來當單元測試的基礎，在寫 module.ts 所關注的細節在這裡也都必須注意到

### [(ngModel)] && FormsModule

再舉個例子來說，測試一個 component 時有可能模板語法中會用到 [(ngModel)]，那在定義 configureTestingModule 時就必須要在 imports 中引入 FormsModule，測試案例才會看懂使用到 [(ngModel)] 的部分

### beforeEach()

![](https://i.imgur.com/9cQC7zP.png)

最後要提一下這個 beforeEach()

beforeEach() 內呼叫 TestBed.configureTestingModule()，代表在每次執行測試之前都能重新建立初始環境，讓每次的測試彼此獨立，不會因為相互影響而造成測試的結果不同，這是單元測試時必須要做好的基本概念之一

## 總結

因為 Angular CLI 是一個一手裝好裝滿的工具，要理解它提供的單元測試環境也是研究了一番，karma 整合了 jasmine 以及 @angular-devkit/build-angular，筆者其實並不熟 jasmine 及 karma，可能得找點時間把這兩個給單獨研究一下

下一篇應該會從 Angular 中最好寫測試的 service 來撰寫，因為 service 應該就是以單純 class 來看它的 method 寫了什麼樣的邏輯，不過如果是有非同步行為的 method 還不知道會發生什麼樣的事 XD
