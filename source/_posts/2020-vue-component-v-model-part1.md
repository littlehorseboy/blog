---
title: Vue 子組件更新父組件資料的『五種』方式（Part 1）
date: 2020-02-02 12:38:02
categories:
- vue
tags:
- vue
- component
cover: https://vuejs.org/images/logo.png
---

Hi，大家好，我是小馬彬，這篇是想要紀錄 Vue 的 Component 在 props 到子組件時，如何有效的更新 props 回到父組件，雖然可以單純的說，就是要從父組件對子組件傳一個可以更新 data 的 function 綁到子組件上，只是 Vue 的語法糖實在太多了阿，一開始看到還真是搞混我，最後總共歸納有五種可變的方式用來讓父子組件溝通屬性

## 1. 父組件定義更新 data 的 method 直接傳給子組件使用（官方不推薦）

第一種在 React 是非常常見的更新父組件 data 的方式，不過 Vue 官方並 `不推薦` 用這種方式

#### 子組件 Input.vue

接取父組件的 value 及更新父組件 data 的 Function

![](https://i.imgur.com/KO8umuN.png)

#### 父組件 Home.vue

在這裡 import Component Input.vue，直接對它 props 更新 data 的 Function，這是 React 最常見的用法，不過 React 比起 Vue 語法糖數量實在少太多了，對一個 Component 的 data(state) 渲染機制也有些微的差異，總之在 Vue 是比較 `不推薦` 這樣子的寫法

![](https://i.imgur.com/UJFAQhu.png)

Vue `不推薦` 這樣子的寫法來傳遞更新 data 的事件 `:handleInputValue="handleInputValue"`
![](https://i.imgur.com/MBETGZ2.png)

#### demo

![](https://i.imgur.com/3XxNglE.gif)

## 2. 父組件綁定 method 到子組件上，子組件使用 `$emit`（官方推薦）

#### 子組件 InputTwo.vue

直接使用 `@input="$emit('handleInputValue', $event.target.value)"`

![](https://i.imgur.com/cP0xn45.png)

#### 父組件 Home.vue

用 `@handleInputValue="handleInputValue"` 掛上事件給子組件

![](https://i.imgur.com/nQ4yta1.png)

Vue `推薦` 這樣子的寫法來傳遞更新 data 的事件 `@handleInputValue="handleInputValue"`
![](https://i.imgur.com/2ml6hhu.png)

#### demo

![](https://i.imgur.com/zYc4Evp.gif)

今天就先介紹最基本的兩種就好，下一篇會再繼續介紹其他語法糖來達成父子組件溝通，直接破梗，就是 .aync 或是 v-model，也是直接對 Component 掛上去使用的寫法

下集待續
