---
title: 讀書筆記 - UML 物件導向系統分析與設計（Part 4） 使用案例圖 - 下
date: 2020-09-13 22:49:59
categories:
- w3HexSchool
- UML
tags:
- w3HexSchool
- UML
cover: https://im1.book.com.tw/image/getImage?i=https://www.books.com.tw/img/001/084/08/0010840839.jpg?v=5dce703d
---

Hi，大家好，本篇文紀錄 [UML 物件導向系統分析與設計](https://www.books.com.tw/products/0010840839) 這本書的節錄

本篇為書本中第五章的後半段，想要從前半段的開始可以先從我的前一篇文 [讀書筆記 - UML 物件導向系統分析與設計（Part 3） 使用案例圖 - 上](https://littlehorseboy.github.io/2020/09/06/202008-reading-notes-uml-part-3/)

---

## 5.3 關係

使用案例的『關係』，最常用到的兩種是『包含關係』（Include）以及『延伸關係』（Extend）

---

## 5.3.1 包含關係

一個使用案例 A 在執行的過程中一定會使用到使用案例 B，那麼案例 A 包含案例 B

![](https://i.imgur.com/pNRHqjR.png)

上圖表示圖書館管理員要進行借書或是還書的動作，必定包含要掃條碼的動作來讓系統讀碼

---

## 5.3.2 延伸關係

在某個條件成立才會產生的延伸

![](https://i.imgur.com/0VjHWCh.png)

顧客下訂單時，遇到沒有存貨的情況，就會進行訂貨

---

## Stereotype

> TIP
> 在上面兩張使用案例圖中，分別在關聯的虛線旁寫上 `<<Include>>` 及 `<<Extend>>`，這個符號 + 文字在 UML 稱為 Stereotype，用來擴充 UML 的詞彙
> 
> `Include` 及 `Extend` 是 UML 本身有針對一些情形所定義的 Stereotype，也可以自行定義 UML 的詞彙來擴充語意，格式: `<<詞彙>>`

---

## 5.3.3 一般化關係

一般化關係可以加使用案例『細分』，就像是父類別有許多子類別繼承的概念

![](https://i.imgur.com/1yFzyec.png)

訂票細分成了『網路訂票』及『ibon 訂票』兩種

---

## 總結

使用案例塑模利用圖形的方式從高層次的觀點來描繪系統功能，適合用來與計劃相關人員解說系統功能

---

## 參考來源

https://www.books.com.tw/products/0010840839
