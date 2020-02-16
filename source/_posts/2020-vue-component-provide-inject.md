---
title: Vue 子組件更新父組件資料 - 特別篇，provide / inject
date: 2020-02-16 08:54:03
categories:
- w3HexSchool
- vue
tags:
- w3HexSchool
- vue
- component
cover: https://vuejs.org/images/logo.png
---

Hi，大家好，我是小馬彬，前兩篇展示了子組件如何有效的將資料更新回父組件，如果還沒看過各種不同的父子組件溝通的方式，可以先看看上一篇『[Vue 子組件更新父組件資料的『五種』方式（Part 2）](https://littlehorseboy.github.io/2020/02/08/2020-vue-component-v-model-part2/)』，這篇要來介紹 provide / inject，它主要的功能就是能讓跨組件的資料傳遞更為方便，如果子組件想要得到父組件的資料，就得用 props 來接

## 一般 props 的方式向子組件傳遞資料

那麼今天**曾祖父組件**的資料，要讓子組件來使用呢？

曾祖父 -> 祖父 -> 父 -> 子

假使 Vue 專案為以下的結構

```
app
├── pages
│   └── Page.vue           (曾祖父)
├── components
│   ├── ListContainer.vue  (祖父)
│   ├── ListSection.vue    (父)
│   └── List.vue           (子)
└── App.vue
```

曾祖父組件準備了資料 list，而後交給了祖父組件

*Page.vue*

![](https://i.imgur.com/bqkTJZH.png)

祖父組件拿到了 list 資料，再轉交給父組件

*Page.vue*
↓
*ListContainer.vue*

![](https://i.imgur.com/6pVwMFh.png)

父組件拿到了 list 資料，最後的轉交給了子組件

*Page.vue*
↓
*ListContainer.vue*
↓
*ListSection.vue*

![](https://i.imgur.com/TC2gIpP.png)

子組件終於得到了 list，可以來把資料列舉出來

*Page.vue*
↓
*ListContainer.vue*
↓
*ListSection.vue*
↓
*List.vue*

![](https://i.imgur.com/6lRGYFj.png)

曾祖父所傳達的東西意境深遠，看來是想要告訴我要享受當下，亦或是我們家族有個仇人叫東東？

![](https://i.imgur.com/YcyS8Ex.png)

---

## 用 provide / inject 來向子組件傳遞資料

回到 Vue，現在筆者不想要經過這麼多組件才能得到 list，這時就能用上 provide / inject 的方式來傳遞

*Page.vue*

將 this.list 裝到 `provide()` 中

![](https://i.imgur.com/lDEEshX.png)

就能將資料給放在了大家都能輕鬆拿得到的地方

以下 ListContainer.vue 及 ListSection.vue 就沒有寫任何 props

*ListContainer.vue*

![](https://i.imgur.com/NEpYB8m.png)

*ListSection.vue*

![](https://i.imgur.com/d9aSZYP.png)

最後 List.vue 直接 `inject` list 進來就能直接使用囉

*List.vue*

![](https://i.imgur.com/Dsx81bm.png)

最後的畫面跟剛剛的一樣

![](https://i.imgur.com/YcyS8Ex.png)

---

只要在較上層的組件中用 provide() 裝資料，這樣不管經過了幾代的子組件，都能直接用 inject，就能將上層組件的 provide() 提供的資料拿到手

這邊要注意的是，這種傳資料方式，就沒有強制型別的宣告了，所以使用上要特別小心，筆者其實也很少用到，是剛好有次因為組件都已經分散得七零八落，需要傳遞很多層才到子組件，就要多寫很多 props 的程式碼，就偷懶用了這個 XD

## 總結

筆者認為一定要非常確定這個資料僅僅是在這個 page 中獨立使用（或是一個完全獨立可拆分的複雜組件），才適合 provide / inject，如果胡亂使用反而可能會造成維護較困難的情況，或是使用的目的會被混淆，不過當然可以透過變數命名改善 XD，如果是更多更多 page 要共用數據，還是會比較建議用 Vuex，在撰寫跟後續維護上會比較容易得到可預期的結果
