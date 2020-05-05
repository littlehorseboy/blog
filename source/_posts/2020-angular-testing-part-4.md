---
title: Angular Testing（Part 4）- Service Test
date: 2020-05-03 22:45:00
categories:
- w3HexSchool
- Angular
tags:
- w3HexSchool
- Angular
- Unit Test
cover: https://angular.tw/assets/images/logos/angular/angular.svg
---

Hi，大家好，我的前一篇文章簡單的敘述了 Angular 的 Service 服務，並且 `ng generate service Value` 來產生一個 value.service.ts，並為它加上了一些 method，有同步的，非同步的 Observable 以及 Promise 共三種來進行測試，今天會延續上一次的程式來了解有依賴的 service，以及將依賴給隔離所使用到的 spy 技巧.

---

## ng generate service

再新建一個 service！直接在終端機輸入 `ng generate service Master`，要利用它來實作依賴其他服務的測試

![](https://i.imgur.com/cpM3iwP.png)

## 將 ValueService 注入 MasterService

![](https://i.imgur.com/S9F5kv1.png)

## 使用依賴的服務來撰寫 method 並設置值到 this.realValue

回顧一下 ValueService 的 getValue()，回傳 `'real value'`

![](https://i.imgur.com/yXKIYU4.png)

```typescript=
// master.service.ts

@Injectable({
  providedIn: 'root'
})
export class MasterService {
  public realValue = '';

  constructor(
    private valueService: ValueService,
  ) { }

  public setValue() {
    this.realValue = this.valueService.getValue();
  }
}
```

## 測試 MasterService 執行 setValue 後是否成功設置

```typescript=
// master.service.spec.ts

describe('MasterService', () => {
  let service: MasterService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(MasterService);
  });

  it('setValue 後 this.value 是否有正常得到值', () => {
    service.setValue();
    expect(service.realValue).toBe('real value');
  });
});
```

### 執行結果

![](https://i.imgur.com/hbAnXQL.png)

還沒用到非同步的情境就可以很單純的直接判斷影響的變數

---

## 訂閱依賴的服務中的 getObservableValue() 來設置值到 this.observableValue

回顧一下 ValueService 的 getObservableValue()，用了 setTimeout 也就是固定在一秒之後會回傳結果

![](https://i.imgur.com/R7DSqim.png)

在 MasterService 撰寫 setObservableValue() 訂閱它並設置到 this.observableValue

```typescript=
// master.service.ts

@Injectable({
  providedIn: 'root'
})
export class MasterService {
  public realValue = '';
  public observableValue = '';

  constructor(
    private valueService: ValueService,
  ) { }

  public setValue() {
    this.realValue = this.valueService.getValue();
  }

  public setObservableValue(): void {
    this.valueService.getObservableValue()
      .subscribe(
        (value) => this.observableValue = value,
      );
  }
}
```

## 測試 MasterService 執行 setObservableValue 後是否成功設置

### 錯誤的方式，無法正常的取得值

```typescript=
  it('setObservableValue 後 this.observableValue 是否有正常得到值', () => {
    service.setObservableValue();
    expect(service.observableValue).toBe('observable value');
  });
```

### 執行結果

![](https://i.imgur.com/bskQKET.png)

它建議你使用 fakeAsync 來處理這個測試，因為有用到 setTimeout 一秒，所以在調用 setObservableValue() 後，使用 `tick(1000)` 來暫緩測試的進行一秒鐘，就能測試成功

## 使用 fakeAsync() + tick()

```typescript=
// master.service.spec.ts

describe('MasterService', () => {
  let service: MasterService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(MasterService);
  });

  it('setValue 後 this.value 是否有正常得到值', () => {
    service.setValue();
    expect(service.realValue).toBe('real value');
  });

  it('setObservableValue 後 this.observableValue 是否有正常得到值', fakeAsync(() => {
    service.setObservableValue();
    tick(1000);
    expect(service.observableValue).toBe('observable value');
  }));
});
```

### 執行結果

![](https://i.imgur.com/OxlHR02.png)

## call 依賴的服務中的 setPromiseValue() 來設置值到 this.promiseValue

回顧一下 ValueService 的 getPromiseValue()，用了 setTimeout 也就是固定在一秒之後會回傳結果，不過待會筆者會將它 Mock 成沒有 setTimeout 的 resolve 結果

![](https://i.imgur.com/OYdzUio.png)

在 MasterService 撰寫 setPromiseValue() 訂閱它並設置到 this.promiseValue

```typescript=
// master.service.ts

@Injectable({
  providedIn: 'root'
})
export class MasterService {
  // 中間省略...
  public promiseValue = '';

  constructor(
    private valueService: ValueService,
  ) { }

  // 中間省略...

  public setPromiseValue(): void {
    this.valueService.getPromiseValue()
      .then((value) => {
        this.promiseValue = value;
      });
  }
}
```

## 用 spyOn 把無法確定回傳時機的 method 給模擬出 `假` 的回傳結果

上面提到的 fakeAsync() + tick(1000) 只適合在確定一秒內會回傳結果的情境上使用，在真實的 api 回傳通常就不可能都有那麼剛好在一秒內會回傳東西給你，這時就可以用上 spyOn，將某個無法確切知道回傳時機的 method，做點小小的 return 值改動

```typescript=
describe('MasterService', () => {
  let service: MasterService;
  let valueServiceStub: Partial<ValueService>; // 型別就算不加 Partial 也可以的

  beforeEach(() => {
    valueServiceStub = new ValueService();

    spyOn(valueServiceStub, 'getPromiseValue').and.returnValue(
      new Promise((resolve) => {
        resolve('promise value stub');
      }),
    );

    TestBed.configureTestingModule({
      providers: [
        { provide: ValueService, useValue: valueServiceStub },
      ],
    });
    service = TestBed.inject(MasterService);
  });

  // 中間省略...

  it('setPromiseValue 後 this.promiseValue 是否有正常得到值', fakeAsync(() => {
    service.setPromiseValue();
    tick();

    expect(service.promiseValue).toBe('promise value stub');
    expect(valueServiceStub.getPromiseValue).toHaveBeenCalledTimes(1);
  }));
});
```

利用 `spyOn(valueServiceStub, 'getPromiseValue').and.returnValue()` 來將 getPromiseValue 給改成直接 resolve 結果的 Promise，測試程式碼的部分就能直接用 tick()，來確保 Promise.then 執行結束，後續的測試就都能正常完成了

> 補充：為什麼用了 tick() 後，就能夠確保 Promise.then 執行完畢了呢？
> 這部分推薦各位 google 關鍵字 **javascript event loop**，應該就能找到一些用 JavaScript 單執行緒解釋一些同步執行，microtasks 還有 macrotasks 之類的說明，就能夠理解這個問題的答案了

### 執行結果

![](https://i.imgur.com/SVZnGp7.png)

## 總結

對注入依賴服務的 service 進行測試，如果是非同步執行的 method，就會用到各種技巧來撰寫測試，畢竟是在測試環境上引入你的單元來跑測試，一些依賴關係複雜的情況就會仰賴測試替身的技巧

service 測試的部分應該就到這篇結束了，下一篇要針對 component 來進行測試的撰寫練習
