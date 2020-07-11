---
title: Puppeteer，該如何判斷登入失敗跳出提示的動作？（順便想記錄第一次用到 Promise.race() XD）
date: 2020-07-11 13:05:41
categories:
- w3HexSchool
- Puppeteer
tags:
- w3HexSchool
- Puppeteer
cover: https://user-images.githubusercontent.com/10379601/29446482-04f7036a-841f-11e7-9872-91d1fc2ea683.png
---

Hi，大家好，最近在用網頁爬蟲 Puppeteer 做一些日常的網頁操作動作，中間有個動作是登入頁面輸入帳密，然後按下 Enter，這時帳密驗證成功後，就會進到下一頁，但是如果帳密有錯誤導致登入失敗呢？

## 爬蟲目標網站說明

筆者準備了一個網頁 https://littlehorseboy.github.io/angular-for-puppeteer/dist/angular-for-puppeteer/

一進去會看到是一個簡易的帳密表單欄位及送出按鈕

帳號 123，密碼 321（只是寫在前端測試用的，沒有連線到資料庫）

<img src="https://i.imgur.com/N6dgRtN.png" style="border: 1px solid #CCC" />

### 如果登入成功

會跳到這個畫面，有成功換頁時就會抓取到的這畫面上的某個元件（程式利用此方式判斷）

<img src="https://i.imgur.com/GHwOoZH.png" style="border: 1px solid #CCC" />

### 如果登入失敗

會跳出 alert，告訴你帳號密碼錯誤

<img src="https://i.imgur.com/1ByT7Xe.png" style="border: 1px solid #CCC" />

---

所以，輸入帳密登入時，會有成功登入跳到新頁面或是跳出帳密錯誤提示的兩種情況

筆者希望讓利用 Puppeteer 撰寫的爬蟲程式，能夠隨機應變這兩種狀況，如果是登入成功，就繼續執行後續要做的事情，如果是登入失敗，就直接停止動作，並關閉瀏覽器

---

## 監聽 dialog

依照 [Puppeteer 官方文件](https://pptr.dev/#?product=Puppeteer&version=v5.0.0&show=api-class-dialog) 的說明，要達成抓到 alert 的內容，得用這種掛事件的方式

```javascript=
page.on('dialog', async dialog => {
  console.log(dialog.message());
  await dialog.dismiss();
  await browser.close();
});
```

然後改成符合筆者這裡需求的方式，用 Promise 包一層，而後立刻執行，讓它是 `Promise {pending}` 的狀態，因為還沒有真的按下表單送出，送出後才會判斷帳密是否正確

```javascript=
const loginDialog = () => new Promise((resolve) => {
  page.on('dialog', async (dialog) => {
    await dialog.dismiss();
    resolve(dialog.message());
  });
});

const startLoginDialog = loginDialog(); // Promise {<pending>} 的狀態
```

OK，事件掛上去，如果直接 .then，又不一定會接到 resolve，可能會出現一大堆紅色字的 UnhandledPromiseRejectionWarning 錯誤訊息，因為有登入成功或登入失敗兩種情況，進入任一種就不會做另一種

1. 登入成功，完全不跳 alert
2. 登入失敗，就反而是抓不到新頁面上的元件

## Promise.race()

原本想破頭這種一堆 Promise 的東西要怎麼寫 if/else 才能漂亮

不知為何一個靈光乍現，想到這個不曾用過的 `Promise.race`，只要任何一個 Promise 參數回了 resolve 或 reject，就會直接抓第一名的回傳內容往下做，還能省掉用不到的 Promise 所耗費的資源時間

最後程式就變成這樣一串（只擷取一部分，非完整程式碼）
```javascript=
// 監聽登入失敗的 alert
const loginDialog = () => new Promise((resolve) => {
  page.on('dialog', async (dialog) => {
    await dialog.dismiss();
    resolve(dialog.message());
  });
});

// 執行監聽，用新變數來等待 Promise {<pending>} 的狀態改變
const startLoginDialog = loginDialog();

console.log('按下 Enter');

await page.keyboard.press('Enter');

// 登入成功 - 跳轉頁面，可以取得頁面上的送出按鈕
// 登入失敗 - 跳出 alert 訊息框，提前結束動作，關閉瀏覽器
const elementOrMessage = await Promise.race([
  page.waitFor('body > app-root > app-record > button'),
  startLoginDialog,
]);

if (elementOrMessage === '帳號或密碼錯誤！') {
  console.log(elementOrMessage);

  console.log('登入失敗，關閉瀏覽器');

  await closeBrowser();

  return;
}
```

程式的 18 行，用 Promise.race([Promise, Promise])，只要其中一個有回傳，就將回傳值拿來做後續判斷，如果是登入失敗的訊息，就關閉瀏覽器不做後續動作

## 登入成功的 log

![](https://i.imgur.com/HHOzITa.png)

## 登入失敗的 log

![](https://i.imgur.com/bqBspJN.png)

## 程式碼

完整專案程式碼在此

https://github.com/littlehorseboy/puppeteer-practice2/tree/20200711

## 總結

會做這種判斷來區分流程，是因為筆者想要把寫好的爬蟲程式分發出去，每個人的帳密都各有不同，所以有可能輸入錯誤，對吧 XD？所以才想要做點防呆，搭配一堆 console.log，來明確知道爬蟲發生了什麼，有些流程上可能還可以玩 Node.js 的 readline 模組，給一些 question 來依照回饋做下一步動作，想著想著又多出一些想玩的東西了 XD
