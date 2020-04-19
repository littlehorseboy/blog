---
title: Angular Testing （Part 1） - 環境說明
date: 2020-04-12 21:40:50
categories:
- w3HexSchool
- Angular
tags:
- w3HexSchool
- Angular
- Unit Test
cover: https://angular.tw/assets/images/logos/angular/angular.svg
---

Hi，大家好，最近正好需要用 Angular 進行開發，目前還是個初心者 XD，好不容易將官方文檔的內容也看了個大概（才怪！一堆都還沒搞懂），就想挑戰看看自動測試，這篇就要用來記錄個寫測試的不同手段，因為 Angular 在使用 `ng generate` 就已經切分成各種不同的 Schematics，有最主要的元件 component、服務 service、還有 module、directive、pipe，目前以筆者這種初心者的觀點認為這幾種東西後續會很常用，尤其是 component、service、module，應該會把測試重點放在這幾樣東西上

## 環境說明

本文中主要是用 Angular CLI 9.0.7，一新建完專案後，就可以直接用 `npm run test` 來執行測試，從 package.json 的 scripts 來看是執行了 `ng test`，測試環境都已經被 CLI 包辦了，筆者就直接用現有的測試環境來開始介紹

Angular CLI 預設使用的是 [Jasmine](https://jasmine.github.io/)，及 [Karma](https://karma-runner.github.io/) 來進行單元測試，用 [protractor](https://www.protractortest.org/) 來進行 E2E 測試

會以單元測試為主來了解，E2E 的部分暫時不會詳細說明到

---

## Jasmine

<img src="https://jasmine.github.io/images/jasmine-white-horizontal.svg" style="background-color: #8A4182; margin-bottom: 16px;">

Jasmine test framework，它是用 JavaScript 來寫測試程式碼的測試框架

```javascript=
describe('A suite', () => {
    it('contains spec with an expecation', () => {
        expect(true).toBe(true);
    });
});
```

寫起來就是這種感覺，用一層描述 describe() 文字內容寫上此測試群組的最大目標，其內部就是測項 it()，文字內容會寫上測試的需求描述

---

## Karma

<img src="https://karma-runner.github.io/assets/img/banner.png" style="margin-bottom: 16px;">

Karma 是一個建立測試環境的函式庫，它會整合真實的瀏覽器或設備等等，還有整合測試框架，讓你能在寫測試後讓測試程式碼在模擬的實際環境中運行，環境的部分就完全依靠 Karma 提供的各種選擇，是非常方便的整合工具

## 測試方法

Angular CLI 一新建專案完本身就已經有整合測試的環境了，就是上述那個測試框架 Jasmine 及整合工具 Karma

每一次使用 ng generate 來產生不同類型的檔案時（如果 component 或 service），其中都會夾帶著 `xxx.spec.ts`，運行測試時會將整個專案的所有 `xxx.spec.ts` 進行測試，測試的方法就是輸入指令 `ng test` 或是 `npm run test` 皆可

執行後會啟動一個測試用的 Chrome，這時它會一直持續的監控測試狀態，不會直接中止，可以立即修改程式碼讓它自動重新進行測試，想要停止監控測試狀態要按下 `ctrl + c` 來中斷

## 總結

結果這篇光是寫套件的說明就給它結束了 XD，還沒有實際的擬定需求案例，因應需求來撰寫測試程式碼，其實是筆者根本還沒想好要測試個什麼才比較能體會到測試的真諦 XD，就是改了 A 卻壞了 B，能依靠測試來直接抓到修改 code 時不經意的害其它地方發生錯誤的這種情況，如果能完善的掌握測試一定能對專案的進行更有安全感！
