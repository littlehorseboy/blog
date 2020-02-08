---
title: Vue 子組件更新父組件資料的『五種』方式（Part 2）
date: 2020-02-08 14:53:03
categories:
- w3HexSchool
- vue
tags:
- w3HexSchool
- vue
- component
cover: https://vuejs.org/images/logo.png
---

Hi，大家好，我是小馬彬，延續上一篇紀錄 Vue 的 Component 在 props 到子組件時，如何有效的更新 props 回到父組件，如果還沒看過基本的兩種父子組件溝通的方式，可以先看看上一篇『[Vue 子組件更新父組件資料的『五種』方式（Part 1）](https://littlehorseboy.github.io/2020/02/02/2020-vue-component-v-model-part1/)』，這篇要來繼續把剩下的三種方式補完，其實可以都算是從基礎的方式衍生下來的，包括下一篇可能會介紹到的 provide / inject，都可以用類似的概念去理解，好，就來開始介紹組件溝通的不同方式吧！

## 3. 父組件在使用子組件時寫上 .sync

.sync 是在 Vue 2.3.0+ 時新增的，筆者認為主要是想要讓子組件在更新父組件時，能夠用一個好閱讀並理解的更新 props 的事件，假使我用 `<Child :title.sync="value" />` 將值用這種方式傳遞給 Child，那我就可以直接在 Child 組件裡面直接使用 `this.$emit('update:title', newTitle)` 這種方式來改變父組件資料，這樣就能一眼看出 $emit 是更新了哪一個 props，這是 Vue 後來推出的方式

##### 子組件 InputThree.vue

![](https://i.imgur.com/bPQaUWi.png)

##### 父組件 Home.vue

![](https://i.imgur.com/PlIMbhL.png)

在父組件直接使用 `:innputValue.sync="inputValue"`，子組件就會有對應的事件 `update:inputValue`

.sync 語法糖轉換後的寫法是這樣

![](https://i.imgur.com/y4U9aOD.png)

![](https://i.imgur.com/7vOPmiB.png)

##### demo

![](https://i.imgur.com/VHxJw0P.gif)


## 4. 父組件使用 v-model，子組件使用 $emit

這裡要展示 `<Child v-model="value" />` 的使用方式，首先要先知道 `v-model` 這個語法糖的意義

`v-model="inputValue"`

v-model 會轉成 :value 及 @input 互相搭配，如下

`:value="inputValue" @input="inputValue = $event"`

通常是給 input 使用，所以使用在父子組件溝通上，筆者覺得算是特殊的用法

##### 子組件 InputFive.vue

![](https://i.imgur.com/pIsgqU7.png)

##### 父組件 Home.vue

![](https://i.imgur.com/or9mfu3.png)

v-model 語法糖轉換後的寫法是這樣

![](https://i.imgur.com/W7x8Heo.png)

![](https://i.imgur.com/M50u33c.png)

##### demo

![](https://i.imgur.com/xvIfP3b.gif)

## 5. 父組件使用 v-model，子組件用 computed + v-model

這又是更變形的做法，利用 computed 的 get、set 將改動值時的結果傳回父組件

##### 子組件 InputSeven.vue

![](https://i.imgur.com/1xij7lg.png)

##### 父組件 Home.vue

![](https://i.imgur.com/j0Q4vsI.png)

##### demo

![](https://i.imgur.com/UeUU2ae.gif)

---

以上的父子組件溝通都是在傳遞 String，如果是在傳遞 Array 或是 Object 時，就得更加小心，因為傳址及傳值的差異，一不小心可能會造成無法預期的結果出現，這五種，我想我最推薦的應該會是第三種 .sync 的方式，語意上比較明瞭

之後應該還會講到 provide / inject，讓父子組件的溝通在跨更多層的情形，能更方便的傳遞要使用的數據
