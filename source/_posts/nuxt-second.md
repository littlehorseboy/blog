---
title: nuxt 實作筆記，掛上 eslint-config-airbnb-base
date: 2019-12-06 10:50:26
tags: hexo
---

## 首先一定要記得 settings.json 要加入 `vue`

![](https://i.imgur.com/QEWfdT7.png)

## 下載 eslint airbnb base

```bash
npx install-peerdeps --dev eslint-config-airbnb-base
```

## 修改 .eslintrc.js

除了把 airbnb-base 加入 extends 之中

也必須把 import/no-unresolved 改成 off

否則 `import Logo from '~/components/Logo.vue'` 的 ~/ 會被 ESLint 當成有問題的路徑

![](https://i.imgur.com/p9MXMYK.png)

## .eslintignore

如果有上傳 dist 資料夾，就是把 .gitignore 的 dist 註解掉

![](https://i.imgur.com/U3cdF4u.png)

就必須要新增 .eslintignore 來 ignore 掉 dist 資料夾的內容

![](https://i.imgur.com/n2yMD35.png)

package.json 的 scripts 之中的 lint 也要補上 .eslintignore，不然 --ignore-path 寫死的狀況下不會吃沒有寫到的檔案

```json
"lint": "eslint --ext .js,.vue --ignore-path .gitignore .eslintignore",
```

![](https://i.imgur.com/ujulaDE.png)

## lint

```bash
npm run lint
```

![](https://i.imgur.com/8pBWNbG.png)
