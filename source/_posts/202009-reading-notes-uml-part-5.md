---
title: 讀書筆記 - UML 物件導向系統分析與設計（Part 5） 活動圖
date: 2020-09-20 21:39:12
categories:
- w3HexSchool
- UML
tags:
- w3HexSchool
- UML
cover: https://im1.book.com.tw/image/getImage?i=https://www.books.com.tw/img/001/084/08/0010840839.jpg?v=5dce703d
---

Hi，大家好，本篇文紀錄 [UML 物件導向系統分析與設計](https://www.books.com.tw/products/0010840839) 這本書的節錄

活動圖可視為傳統流程圖的延伸，一般可以對以下幾種來塑模

1. 企業處理工作流程
2. 使用案例的流程分析
3. 商務規則邏輯塑模
4. 功能處理
5. 使用者介面流程

---

## 7.2 符號

活動圖由節點（Node）及邊（Edge）所構成

* 節點（Node）：表示動作、控制，或是物件
* 邊（Edge）：表示活動的流向

### 活動起始節點（Activity Start）

代表一個活動的開始

![](https://i.imgur.com/F6mVB3r.png)

### 活動終止節點（Activity Final）

代表一個活動的結束

![](https://i.imgur.com/ztVOurN.png)

### 動作（Action）節點

表示所執行的動作，一序列的動作稱為活動

![](https://i.imgur.com/SYjgjoe.png)

### 控制流程（Control Flow）

表示一個動作到另一個動作的流向

![](https://i.imgur.com/kk8dwMB.png)

### 物件流程（Object Flow）

動作旁邊加上小方塊表示此動作是輸出還是輸入物件

![](https://i.imgur.com/vXJadF5.png)

### 分岔（Fork）節點

接續的動作分成兩個以上同步進行的動作

![](https://i.imgur.com/qVdWN4K.png)

### 結合（Join）節點

等所有的動作都完成了，才會繼續執行接下來的動作

![](https://i.imgur.com/oQWyYr1.png)

### 決策 / 合併節點（Decision/Merge）

一個進入的流向，而後有兩個以上離開的流向可以選

![](https://i.imgur.com/9KaZZx8.png)

### 物件（Object）節點

表示活動是輸入資料還是輸出資料

![](https://i.imgur.com/gIpM05S.png)

### 送出訊號（Sending Signal）

事件發生所產生的訊號

![](https://i.imgur.com/MfJg2Kr.png)

### 接收訊號（Receiving Signal）

一個動作的執行是因為接收了什麼樣的事件

![](https://i.imgur.com/PaSfA50.png)

### 流程終止（Flow Final）節點

流程的結束

![](https://i.imgur.com/KdXZk1h.png)

### 動作之前置條件 / 後置條件

動作發生前或是發生後所需滿足的條件

![](https://i.imgur.com/uvFWHF9.png)

### 時間訊號（Time Signal）

時間，像是排程的應用

![](https://i.imgur.com/wRQbkCS.png)

### 可中斷區（Interrupted Activity Region）

區域內的動作可能被中斷

![](https://i.imgur.com/xDNMOh5.png)

### ~~中斷~~（Interrupting Edge）

表示中斷活動後的流向

### ~~連結器~~（Connector）

連結不連續的流向

---

## 參考來源

https://www.books.com.tw/products/0010840839
