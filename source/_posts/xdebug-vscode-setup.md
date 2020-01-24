---
title: 設定 Xdebug，最後用 VSCode 的偵錯工具啟動
date: 2019-12-09 21:10:41
categories:
- apache
- php
- xdebug
tags:
- apache
- php
- xdebug
---

## Xdebug 下載

首先進入這一頁

https://xdebug.org/wizard

![](https://i.imgur.com/sbslusN.png)

將 phpinfo() 的內容全選複製貼到那個 textarea 之中，然後按下 Analyse

![](https://i.imgur.com/jJ7tG3M.png)

這時檢測完各種版本號後，跟著最底下的使用說明動手做

![](https://i.imgur.com/4gRPh6Z.png)

![](https://i.imgur.com/hOKZoma.png)

1 ~ 2 下載然後丟到 ext 資料夾

3 要你修改 php.ini

但是還要再多補幾行

![](https://i.imgur.com/ZAa0MGJ.png)

## VSCode 偵錯工具啟動

筆者是用 VSCode，這邊要設定 php.exe 的路徑到 settings.json 裡面

![](https://i.imgur.com/m1GxAaR.png)

就可以直接利用 VSCode 點個幾下來 debugger 了

![](https://i.imgur.com/nL7AJzK.png)

![](https://i.imgur.com/RnwaMh1.png)
