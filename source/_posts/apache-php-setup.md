---
title: Apache 及 PHP 環境架設
date: 2019-12-09 11:24:37
categories:
- apache
- php
tags:
- apache
- php
cover: https://www.php.net/images/logos/php-logo.svg
---

## Apache 2.4.41 下載

https://httpd.apache.org/docs/current/platform/windows.html#down

![](https://i.imgur.com/Sbn80cY.png)

載完得到壓縮檔後將 Apache24 的資料夾放在 C:

![](https://i.imgur.com/U2ERwQm.png)

## 配置環境變數

![](https://i.imgur.com/aemnaIE.png)

## httpd.conf

這裡要注意 Define SRVROOT 是不是指定著正確的路徑

![](https://i.imgur.com/XmzKftK.png)

## 將應用程式加進服務

![](https://i.imgur.com/uWRdCFY.png)

就能直接啟動它了

![](https://i.imgur.com/SwkdQoH.png)

然後到瀏覽器輸入 localhost 有 Apache Haus 的頁面就表示安裝成功了

---

## PHP 7.4.0 下載

https://windows.php.net/download#php-7.4

![](https://i.imgur.com/CNmVXp6.png)

在 C: 蓋的資料夾 PHP7.4.0，然後把壓縮檔內容放進去

## 配置環境變數

![](https://i.imgur.com/CEox5v5.png)

就可到 PowerShell 測試 php -v 指令有沒有正常跳出訊息

## httpd.conf

增加 php 相關的模組及設定到 httpd.conf

![](https://i.imgur.com/zSKM1bJ.png)

![](https://i.imgur.com/4TeXdSt.png)

![](https://i.imgur.com/rPmuYpr.png)

## php.ini

載下來的 php 資料夾內容裡面並沒有 php.ini

得用 php.ini-development 複製一份來修改檔案名稱

![](https://i.imgur.com/uO50PmP.png)

打開 php.ini 來進行修改

![](https://i.imgur.com/FR6tFKx.png)

![](https://i.imgur.com/YAalcTH.png)

## 修改 localhost 指的路徑到自己想要存放網站的地方

開啟 httpd.conf，原本的 ${SRVROOT}/htdocs 改成自己的目錄

![](https://i.imgur.com/xdPVNSD.png)

這樣就 OK 了，可以去該目錄底下玩一個 index.php 並 `echo phpinfo();` 來看看
