---
title: C#，又來找你玩了！ - Visual Studio 重新認識
date: 2020-02-22 13:06:58
categories:
- w3HexSchool
- C#
tags:
- w3HexSchool
- C#
- Visual Studio
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/C_Sharp_wordmark.svg/225px-C_Sharp_wordmark.svg.png
---

Hi，大家好，我是小馬彬，最近心血來潮的想要重溫 C#，想想過去在開發 ASP.NET MVC 網站時都是硬幹，新建專案，跟著專案架構亂打一通，有問題就 google 找到解就貼上去，也不管那些解到底使用了什麼樣的核心模組，所以一整個網站中有非常多程式碼是在不清不楚，但是可以 run 的狀況下完成的，這樣說起來好像很危險 XD，留下了一堆不明不白，不過其實算是依靠 C# 強型別的特性，以及 IDE 的強大，穩定性應該還是有的，只是可讀性以及日後如果要維護去修改時，一定會很容易改 A 壞 B，最近重新拜讀了 SOLID，加上這幾年在寫 JS 的模組化也算小有心得，毅然決然地想要把過去搞得很不順手的 C# 給重新研究一番

## 古人有云：「工欲善其 code，必先利其 IDE」（明明是我瞎掰的）

所以就是好好的了解 IDE 一番，也就是 Visual Studio 啦，官方文檔要瞄過一遍，每次有改版的說明也得好好了解，其實是因為看過別人實際重構的方式，點來點去就能拆解組合，抽出介面等等，覺得 IDE 真的很有投資價值

## 站在巨人的肩膀上（抱 Visual studio Docs 的大腿？）

這篇應該是記錄非常基礎的 C# 練習過程，會從 Visual Studio 安裝頁面上的連結開始[讀](https://docs.microsoft.com/en-us/visualstudio/get-started/visual-studio-ide?view=vs-2019)（當然還是會選擇性的挑跟 C# 或是 Web 較相關的來讀），裝好了最新的 Visual Studio 2019，現在開始要狠狠的將基礎給打好，好好地將官方文檔給讀過一遍，這種讀文件的習慣在寫前端的時期才真的有養成，且確實在開發時省掉了非常多的麻煩，不過也有可能在菜鳥時期的我，想要看官方文檔反而更是霧煞煞，因為對於程式碼通常可以做到什麼事情都還沒有通盤了解 XD

## 開始刻意練習

首先建立新專案，要開個主控台應用程式

![](https://i.imgur.com/DM2QCQC.png)

設定專案名稱及存放位置

![](https://i.imgur.com/Z7XtAmD.png)

建立後就會看到這個畫面了

![](https://i.imgur.com/RhkZNRO.png)

馬上來改寫基本的 Console.WriteLine() Console.ReadLine()

![](https://i.imgur.com/L9DkNyp.png)

ctrl + F5 啟動後就能與黑黑的指令介面互動了

![](https://i.imgur.com/RERUKsE.gif)

---

## 特殊 IDE 使用技巧

以前根本不以為意的重構技巧

### 變數的命名修改

![](https://i.imgur.com/Ttz30Z4.gif)

---

繼續跟著官方文檔增加取得 DateTime 的程式碼

![](https://i.imgur.com/tKobL8D.png)

執行結果

![](https://i.imgur.com/NwLvMTZ.png)

這樣排好像不太好看，我要讓時間的顯示文字成為一行

---

## 快捷鍵，相符文字新增至多重選取再一起修改

程式碼中的三行 `Console.WriteLine` 要改成 `Console.Write`

懶人如我不想要一個一個改，在 VSCode 中有個 `ctrl + d` 超方便能用相符文字來往下選取

在 Visual Studio 查到一樣的功能的快捷鍵是 `Shift + Alt + .`

對要更改的單字目標，按三次 `Shift + Alt + .`（如果滑鼠不是雙擊來選是按四次）

![](https://i.imgur.com/d4x1WZ8.gif)

---

## 利用 IDE 的功能來重構

左方有個螺絲起子，點開來就能選取 IDE 所建議的重構方式

![](https://i.imgur.com/DTFh2oF.gif)

重構後不影響整體行為，還有剛剛 WriteLine 更改成 Write 的效果也一併在這呈現

![](https://i.imgur.com/1kB0Cym.gif)

## 總結

今天就寫到這邊，之後會繼續跟著官方文檔的其他篇幅來實作，一方面是精進 Visual Studio 的寫 code 技巧，也更多的去了解 C# 的 class 或核心模組，目標是要將 SOLID 給理解的更透徹，這篇寫的 code 當然完全沒有去管那些原則 XD，還有最終目標就是要能寫單元測試，了解什麼樣是可測試的 code
