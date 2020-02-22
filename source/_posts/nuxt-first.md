---
title: nuxt 實作筆記，從 create-nuxt-app 到架上 github pages
date: 2019-12-06 09:39:15
categories:
- nuxt
- github pages
tags:
- nuxt
- github pages
cover: https://nuxtjs.org/logos/nuxt.svg
---

因為工作上的需要，開發開始研究 nuxt ssr

所以特別在這邊紀錄要掛到 github pages 的過程

期望未來掛到 Production 環境時能夠起到作用

## 第一步

```bash
npx create-nuxt-app nuxt-test20191206
```

記錄一下 create-nuxt-app 的版本

![](https://i.imgur.com/YkumpVf.png)

產生的整包專案結構

![](https://i.imgur.com/ZN9IPrg.png)

## 本地測試伺服器

此時就可以 `npm run dev` 來隨意進行更動然後觀察變化了

## 修改 pages 資料夾

![](https://i.imgur.com/YAuMOOH.png)

增加 user 資料夾及 index.vue

內容除了原本的預設內容多放了互相連結的 `nuxt-link`

## github pages 設置

此時還不能直接丟上 github pages，會有一些毛病要修改

package.json 增加這兩段

```json
"build:gh-pages": "cross-env DEPLOY_ENV=GH_PAGES nuxt build",
"generate:gh-pages": "cross-env DEPLOY_ENV=GH_PAGES nuxt generate"
```

安裝 cross-env

```bash
npm i -D cross-env
```

nuxt.config.js 增加 router base

```javascript
router: {
  base: process.env.DEPLOY_ENV === 'GH_PAGES' ? '/nuxt-landing-page/dist/' : '/'
}
```

此時可以執行打包成靜態檔案的指令

```bash
npm run generate:gh-pages
```

.gitignore 記得把 dist 給註解掉

另外還要新增 404.md，讓首頁的路由可以正常

![](https://i.imgur.com/XWvFWyz.png)

## git push

最後應該就能在 github pages 上看到兩頁互切

![](https://i.imgur.com/563awVW.gif)
