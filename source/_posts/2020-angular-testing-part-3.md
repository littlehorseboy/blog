---
title: Angular Testing（Part 3）- Service Test
date: 2020-04-26 13:06:49
categories:
- w3HexSchool
- Angular
tags:
- w3HexSchool
- Angular
- Unit Test
cover: https://angular.tw/assets/images/logos/angular/angular.svg
---

Hi，大家好，前兩篇文章簡單介紹了 Angular CLI 一手裝好裝滿的單元測試環境，分別淺淺的講了 Jasmine 及 Karma，如何啟動測試，Angular CLI 大概的單元測試配置檔，以及 TestBed.configureTestingModule() 定義了類似 @NgModule 應用的來打造測試環境，今天要針對 service 的測試進行練習

## Service 服務

服務是定義了特殊用途的 class，從伺服器獲取資料、驗證使用者輸入或直接往控制檯中寫日誌等工作會委託給各種服務

服務就是一個 class，只是在 Angular 之中此 class 帶有 @Injectable 這個 [decorator](https://www.typescriptlang.org/docs/handbook/decorators.html)，利用 Angular 獨有的模組化方式來使用這個服務，透過依賴注入來幫你更容易地將應用邏輯分解為服務

## ng generate service

就來建立一個 service 吧！直接在終端機輸入 `ng generate service Value` 即可

![](https://i.imgur.com/ekCbWFJ.png)

就會得到這兩個 .ts 檔

![](https://i.imgur.com/BYfvrND.png)

![](https://i.imgur.com/XWhzrkM.png)

service 產生後的初始狀態就是這樣，帶有 @Injectable 的 class，就可以直接對 class 填進所需要的 property 或 method 來進行設計，測試檔的部分寫好了 TestBed 設定完並 inject 進 ValueService 的實體，能直接進行撰寫測試項目

### 對同步執行的 pure function 回傳值進行測試

這個 function 就是同步的執行且 return 值

在 value.service.ts 撰寫一個 method

```typescript=
  public getValue(): string {
    return 'real value';
  }
```

在 value.service.spec.ts 撰寫相應的測試

```typescript=
  it('getValue should return real value', () => {
    expect(service.getValue()).toBe('real value');
  });
```

執行測試

![](https://i.imgur.com/GriS5Hh.png)

### 對非同步執行的 Observable 回傳值進行測試

在 value.service.ts 撰寫一個假裝 1 秒後回傳值的 Observable

```typescript=
  public getObservableValue(): Observable<string> {
    return new Observable((subsriber: Subscriber<string>): void => {
      setTimeout((): void => {
        subsriber.next('observable value');
        subsriber.complete();
      }, 1000);
    });
  }
```

在 value.service.spec.ts 撰寫相應的測試

因為是非同步回傳的行為，必須要在測試的函式中使用 `done: DoneFn` 這個參數進行 callback 呼叫來告知測試要結束的時機

測試 Observable 就直接訂閱 subscribe 下去，在 done、error 或 complete 的函式進行測試

```typescript=
  it('getObservableValue should return value from observable', (done: DoneFn) => {
    service.getObservableValue().subscribe(
      (value) => {
        expect(value).toBe('observable value');
        done();
      },
    );
  });
```

執行測試

![](https://i.imgur.com/wvOklnG.png)

### 對非同步執行的 Promise 回傳值進行測試

在 value.service.ts 撰寫一個假裝 1 秒後回傳值的 Promise

```typescript=
  public getPromiseValue(): Promise<string> {
    return new Promise((resolve): void => {
      setTimeout((): void => {
        resolve('promise value');
      }, 1000);
    });
  }
```

在 value.service.spec.ts 撰寫相應的測試

跟剛才的 Observable 一樣有非同步回傳的行為，在測試函式中使用 `done: DoneFn` 來告知測試要結束的時機

```typescript=
  it('getPromiseValue should return value from a promise', (done: DoneFn) => {
    service.getPromiseValue()
      .then((value) => {
        expect(value).toBe('promise value');
        done();
      });
  });
```

執行測試

![](https://i.imgur.com/bZjiJkv.png)

### 非同步行為如果測試沒有執行到 done()

```typescript=
  it('getPromiseValue should return value from a promise', (done: DoneFn) => {
    service.getPromiseValue()
      .then((value) => {
        expect(value).toBe('promise value');
        // done();
      });
  });
```

執行的測試會跑了 5 秒後告訴你測試 timeout 並失敗了

![](https://i.imgur.com/gWw2xTG.png)

所以非同步行為的測試撰寫要特別注意，偏偏網頁前端通常都有大量的非同步行為，要撰寫針對非同步行為來寫測試的這種情形一定會常常遇到

### 非同步行為的測試有一個 **雷**

這裡把測試項目中的 done() 給拿掉，並且連測試函式的參數 `done: DoneFn` 都不放

```typescript=
  it('getPromiseValue should return value from a promise', () => {
    service.getPromiseValue()
      .then((value) => {
        expect(value).toBe('promise value');
      });
  });
```

結果...

![](https://i.imgur.com/7KegQC8.png)

測試居然綠油油的通過了！？

等等等，仔細看一下瀏覽器上顯示的文字

![](https://i.imgur.com/GgDacB1.png)

有一條測試項目標明 `SPEC HAS NO EXPECTATIONS`，代表這個測試項目完全沒執行到任何 expect 的函式，等於是一段無效的測試，這應該會是未來需要特別留意的，不確定為何沒有設計成跳 failure 並顯示紅色來讓它更容易察覺，也可能有其他地方可以更改設置，留待未來覺得這種顯示雷到受不了時就會解決它

## 總結

今天只簡單的對最基本會使用的 method 來撰寫測試，回顧一下今天這篇文的一些重點

* 測試 service 要先用 TestBed 來 inject 取得 service 測試實體
* 測試撰寫遇到同步行為時很一般，但有非同步行為時要記得告知測試執行 done() 來結束測試，否則會 timeout 測試失敗
* 要注意 `SPEC HAS NO EXPECTATIONS` 這個關鍵字，雖然是測試項目列顯示是綠色的，但這可是無效的測試無誤

下一篇還是寫對 service 進行測試，是關於有依賴的 service，有時會需要將依賴給隔離而用到 spy，為了讓單元測試能夠獨立執行而不會被其他依賴干擾所使用的技巧
