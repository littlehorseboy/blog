---
title: T-SQL NULLIF - 比對如果相符合就回你一個 NULL 的函式
date: 2020-08-16 20:22:38
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://cdn.worldvectorlogo.com/logos/microsoft-sql-server.svg
---

Hi，大家好，這次要介紹一個在 T-SQL 上的函式，就先直接來看看範例

## NULLIF

```sql=
SELECT NULLIF('', '')
SELECT NULLIF(0, 0)
SELECT NULLIF(123, '')
```

執行結果

![](https://i.imgur.com/96hJJhA.png)

可以看到使用 NULLIF 函數，如果兩個參數相符合，會回傳 NULL，如果不符合，就會回傳第一個參數的值

題外話是覺得 NULLIF 這個函式的命名還蠻妙的 XD，第一次看到沒能直接想到居然是這樣子的功用

---

## 用 NULLIF 來替代 CASE

接下來是示範 NULLIF 與 CASE 兩者都可以達到一樣的效果的寫法

```sql=
DECLARE @a int = 0;
DECLARE @b int = 5;

SELECT
  CASE WHEN @a = 0 THEN NULL ELSE @a END AS a,
  CASE WHEN @b = 0 THEN NULL ELSE @b END AS b

SELECT
NULLIF(@a, 0) AS a,
NULLIF(@b, 0) AS b
```

執行結果

![](https://i.imgur.com/cYuK0rU.png)

兩者比較後可以看到 NULLIF 要撰寫的程式碼減少非常多，不過要注意到，也只有真的有需要回傳 NULL 的情境下，才是用 NULLIF，另一種 CASE 能有更多的靈活性

那需要用到 NULL 的情境真的很多嗎？我也不知道 XD，但最近有遇到一種情境用到可以拿來分享

## 用 NULLIF 來排除 JSON_VALUE 會遇到不合法 JSON 格式的錯誤

會促使想要記錄這個函式的原因，就是因為剛好遇到了資料表內有一欄是存放文字型態的，真的有資料的時候是放 JSON，不然居然是空字串 XD，然後遇到空字串要用 JSON_VALUE 解析就發生了大問題

先回憶一下 JSON_VALUE 函式的用法，它是用來解析 JSON 取得單個 property 的函式

### JSON_VALUE 解析 JSON 的一般情況

```sql=
SELECT JSON_VALUE('{ "a": 123 }', '$.a');

SELECT JSON_VALUE('{}', '$.a');
```

![](https://i.imgur.com/f5DqYRB.png)

### JSON_VALUE 解析空字串報錯

那麼如果第一個參數丟了空字串會怎麼樣呢？

```sql=
SELECT JSON_VALUE('', '$.a');
```

![](https://i.imgur.com/MgVg56S.png)

就直接整個報錯

### JSON_VALUE 解析 NULL

後來有想到可以把空字串轉成 NULL，因為 JSON_VALUE 遇到 NULL 來解析是合法的，就會安全的回傳 NULL

```sql=
SELECT JSON_VALUE(NULL, '$.a');
```

![](https://i.imgur.com/X9oRtZY.png)

### JSON_VALUE 解析字串搭配 NULLIF

這時就想要把遇到空字串的資料通通轉成 NULL，一開始寫落落長的 CASE WHEN THEN，然後就查到這個簡短寫法 NULL，最後就成了這樣

```sql=
SELECT JSON_VALUE(NULLIF('', ''), '$.a');
```

![](https://i.imgur.com/2YCSesz.png)

這樣就能有效避免 JSON_VALUE 遇到空字串而報錯的情況

### 再搭配 ISNULL 來把 NULL 轉成空字串

```sql=
SELECT ISNULL(JSON_VALUE(NULLIF('', ''), '$.a'), '');
```

### 也能用函式 ISJSON 判斷字串是否為合法 JSON，可以搭配 CASE 來改寫

```sql=
DECLARE @a nvarchar(MAX) = '{ "a": 123 }';
DECLARE @b nvarchar(MAX) = '';

SELECT CASE WHEN ISJSON(@a) = 1 THEN JSON_VALUE(@a, '$.a') ELSE '' END;
SELECT CASE WHEN ISJSON(@b) = 1 THEN JSON_VALUE(@b, '$.a') ELSE '' END;
```

## 結尾

本篇是剛好遇到第一次用過的函式 NULLIF，才特別紀錄一下，與 CASE 相比較，一樣的需求搭配 JSON_VALUE 組合出的不一樣寫法
