---
title: E2E Testing（jest + puppeteer 環境設置）
date: 2020-05-17 22:27:46
categories:
- w3HexSchool
- Puppeteer
tags:
- w3HexSchool
- Puppeteer
cover: https://user-images.githubusercontent.com/10379601/29446482-04f7036a-841f-11e7-9872-91d1fc2ea683.png
---

Hi，大家好，最近想要用爬蟲來做一些重複的網頁操作動作，預期要做的動作是進入網頁，輸入帳密登入，然後操作表單，最後送出表單

本篇是接續著前一篇 [Puppeteer，簡單易用的 Node.js 爬蟲](https://littlehorseboy.github.io/2020/05/10/2020-puppeteer-beginner/)，裡面安裝了 Puppeteer，撰寫小小的腳本，來讓爬蟲自動操作了 Google 搜尋首頁，打字到 input 裡，並按下 Enter 鍵，完成搜尋的動作

那怎麼會講到 E2E Testing 呢？筆者是認為 E2E Testing 實際上就是 `爬蟲 + 測試函式庫` 的組合技，那既然都要玩爬蟲，那乾脆就把測試函式庫也 + 上去吧！正好最近也在一點一點的琢磨測試的技能，想必遲早會用得上

這篇文主要是敘述將 `Jest` + `Puppeteer` + `TypeScript` 的專案環境建置起來

---

## 首先是建立一個 TypeScript 的 Node.js 專案配置

如果想要一個有 ESLint + Jest + TypeScript 的 Node.js 配置，剛好最近發現，可以參考 [NestJS](https://nestjs.com/) 的專案配置，NestJS 就是一個用 TypeScript 組織架構起來的 Node.js 後端框架，所以就裝了 @nestjs/cli 來創一個專案，然後開始抄走配置 XD，去蕪存菁留下需要的（其實是只留下能理解的，還有超多不懂得怎麼用 XD）

### 專案配置設定的參考來源

1. @nestjs/cli 安裝後使用指令 `nest new [project-name]` 產生的專案架構

   ![](https://i.imgur.com/gy72yxB.png)

2. @nestjs/schematics，從 cli 新建專案裡的 package.json 挖到的配置

   https://github.com/nestjs/schematics

3. [Jest 官方說明](https://jestjs.io/docs/en/puppeteer)

   Jest 官方中有使用 Jest + Puppeteer 的說明，這也是我會選擇使用 Puppeteer 的原因，第一段說明要安裝 jest-puppeteer，不過因為有 TypeScript 相關的設定需求，還得進到 Jest Puppeteer 的 github 看 README.md 才有

   https://github.com/smooth-code/jest-puppeteer

### 最後在專案中裝的所有東西

| Node Package | 說明 |
| ----------------------- | ----------------------- |
| @types/jest | （TypeScript 類型定義檔） 斷言函式庫 [jest](https://github.com/facebook/jest) |
| @types/expect-puppeteer | （TypeScript 類型定義檔） 專為 Puppeteer 測試而設計的斷言函式庫 [expect-puppeteer](https://github.com/smooth-code/jest-puppeteer/tree/master/packages/expect-puppeteer) |
| @types/jest-environment-puppeteer | （TypeScript 類型定義檔） 整合 Jest + Puppeteer [jest-environment-puppeteer](https://github.com/smooth-code/jest-puppeteer/tree/master/packages/jest-environment-puppeteer)，可以直接 global 取用 Puppeteer 提供的屬性，例如 browser、page 等等 |
| @types/node | （TypeScript 類型定義檔） Node.js 核心模組定義 |
| @types/puppeteer | （TypeScript 類型定義檔） Puppeteer API 定義 |
| @typescript-eslint/eslint-plugin | 搭配 @typescript-eslint/parser，用於 ESLint 的 TypeScript 規則集 |
| @typescript-eslint/parser | ESLint 解析器 |
| eslint | 靜態代碼分析工具 |
| eslint-plugin-import | ESLint 標配的規則集 |
| jest | JavaScript 測試框架 |
| jest-puppeteer | Jest + Puppeteer |
| puppeteer@1.20.0 | 簡單易用的 Node.js 爬蟲 |
| ts-jest | 讓 Jest 能夠執行 TypeScript 的 .ts 檔案 |
| typescript | JavaScript 嚴格超集 |

### npm i 安裝一堆

```bash
npm i -D @types/jest @types/expect-puppeteer @types/jest-environment-puppeteer @types/node
```

```bash
npm i -D @types/puppeteer @typescript-eslint/eslint-plugin @typescript-eslint/parser
```

```bash
npm i -D eslint eslint-plugin-import jest jest-puppeteer
```

```bash
npm i -D puppeteer@1.20.0 ts-jest typescript
```

### 為什麼是指定安裝 puppeteer@1.20.0 呢？

撰寫這篇文時，能安裝的到 Puppeteer 最新版本是 3.0.4 版，但是筆者剛好發現有些電腦在安裝後無法正常啟動 chromium，以及搭配 jest-puppeteer 的時候，會發生測試總是 fail 的奇怪情況，兩種情況都在直接降版後解決 XD，如果有讀者知道為什麼會發生這種怪事，就要靠你分享一下啦~

### 安裝了所有需要的套件之後

部分套件要配置相應的設定檔

1. .eslintrc.js
2. .gitignore
3. jest.config.js
4. tsconfig.json

就這四個，參考著更上面 ↑ 寫到的專案配置設定的參考來源，來撰寫設定檔，大多是整個抄，有些綜合起來，有些微微照喜好調整

#### .eslintrc.js

```javascript=
// .eslintrc.js

module.exports = {
  parser: '@typescript-eslint/parser',
  parserOptions: {
    sourceType: 'module',
  },
  plugins: ['@typescript-eslint/eslint-plugin'],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/eslint-recommended',
    'plugin:@typescript-eslint/recommended',
  ],
  root: true,
  env: {
    node: true,
    jest: true,
  },
  rules: {
    '@typescript-eslint/interface-name-prefix': 'off',
  },
};
```

#### .gitignore

```bash=
// .gitignore

# compiled output
/dist
/node_modules

# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*

# OS
.DS_Store

# Tests
/coverage
/.nyc_output

# IDEs and editors
/.idea
.project
.classpath
.c9/
*.launch
.settings/
*.sublime-workspace

# IDE - VSCode
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
```

#### jest.config.js

```javascript=
// jest.config.js

module.exports = {
  moduleFileExtensions: [
    'js',
    'json',
    'ts',
  ],
  rootDir: './src',
  testEnvironment: 'node',
  transform: {
    '^.+\\.(t|j)s$': 'ts-jest',
  },
  testRegex: ['.(test|spec).ts$'],
  coverageDirectory: '../coverage',
  verbose: true,
  bail: true,
  testPathIgnorePatterns: [
    './node_modules/',
  ],
  preset: 'jest-puppeteer',
};
```

#### tsconfig.json

```json=
// tsconfig.json

{
  "compilerOptions": {
    "module": "commonjs",
    "declaration": true,
    "removeComments": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "target": "es2017",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./",
    "incremental": true
  },
  "exclude": ["node_modules", "dist"]
}
```

還要將 package.json 的 scripts 改一下

![](https://i.imgur.com/7ZQvnFm.png)

之後就用 `npm run test` 來執行測試

那目前，專案的整個環境配置都已經 OK 了

![](https://i.imgur.com/I8OY98F.png)

## 是時候要撰寫爬蟲 + 測試的程式碼了

這次還是要利用 Google 搜尋來展示爬蟲，但不同的是這次有測試的功能

動作是要在 Google 進行關鍵字搜尋 'horse'，然後檢查是不是有得到預期的查詢結果

新增一個 src/google.spec.ts

以下就直接貼上程式碼

```typescript=
// google.spec.ts

import { launch, Browser, Page } from 'puppeteer';

describe('Google 首頁進行搜尋 horse', () => {
  let browser: Browser;
  let page: Page;

  beforeAll(async () => {
    browser = await launch({
      headless: false,
      slowMo: 100,
    });
    page = await browser.newPage();

    await page.goto('https://google.com');
  });

  afterAll(async () => {
    await browser.close();
  });

  test('搜尋欄位輸入 "horse"，然後測試有沒有成功輸入', async () => {
    const input = await page.$('#tsf > div:nth-child(2) > div.A8SBwf > div.RNNXgb > div > div.a4bIc > input');
    await input?.type('horse');

    const titleValue = await page.evaluate((el: HTMLInputElement) => el.value, input);

    await expect(titleValue).toBe('horse');
  });

  test('成功輸入後，按下 enter，到搜尋結果的頁面，測試看看有沒有翻譯出 "馬"', async () => {
    await page.keyboard.press('Enter');

    await page.waitForNavigation();

    const span = await page.$('#tw-target-text > span');

    const spanText = await page.evaluate((el: HTMLSpanElement) => el.textContent, span);

    await expect(spanText).toBe('馬');
  });
});
```

這段是可以通過測試的喔

![](https://i.imgur.com/YDdMNS9.png)

## 附上原始碼

https://github.com/littlehorseboy/puppeteer-jest

## 總結

在這篇文主要能了解的是 `Jest` + `Puppeteer` + `TypeScript` 的 Node.js 專案環境配置

寫的比預想的還要長，只是配置個專案 XD，不過目前離達成目標越來越近了，目標就是要省下重複的網頁操作動作
