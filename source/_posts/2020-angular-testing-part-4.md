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

Hi，大家好，我的前一篇文章簡單的敘述了 Angular 的 Service 服務，並且 `ng generate service Value` 來產生一個 value.service.ts，並為它加上了一些 method，有同步的，非同步的 Observable 以及 Promise 共三種來進行測試，今天會延續上一次的程式來了解有依賴的 service，以及將依賴給隔離所使用到的 spy 技巧

## ng generate service

再新建一個 service！直接在終端機輸入 `ng generate service Master`

![](https://i.imgur.com/cpM3iwP.png)

## 將 ValueService 注入 MasterService

![](https://i.imgur.com/S9F5kv1.png)

## 使用依賴的服務來撰寫 method 並設置值到 this.realValue

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

## 訂閱依賴的服務中的 getObservableValue() 來設置值到 this.observableValue

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

## 總結

對注入依賴服務的 service 進行測試，如果是非同步執行的 method，就要利用到各種技巧

下一篇要對 component 進行測試的撰寫練習
