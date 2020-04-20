---
title: Angular Testing （Part 2） - 再進階一點點的環境說明
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

首先就從根目錄位置的 `angular.json` 開始看起，筆者的檔案應該是沒有異動過，是在 83 行的位置開始

![](https://i.imgur.com/MiZ0Rig.png)

有個 `"test"` 的設置，在這主要要特別拿來說明的是 `"main": "src/test.ts"` 及 `"karmaConfig": "karma.conf.js"`，他們就是你的設置檔

src/ 目錄下的 test.ts 和 根目錄下的 karma.conf.js

![](https://i.imgur.com/pZqFEio.png)

test.ts 的 getTestBed() 是用來動態建立一個模擬 @NgModule 的 Angular 測試模組，看起來這邊用 getTestBed() 打造出類似 app.module.ts 的基底

23 行 `require.context('./', true, /\.spec\.ts$/);` 會遞迴你整個專案的 xxx.spec.ts

![](https://i.imgur.com/Z671Vtm.png)

karma.conf.js 整合了測試框架 jasmine，以及 angular 相關的環境

