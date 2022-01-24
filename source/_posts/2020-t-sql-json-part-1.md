---
title: T-SQL OPENJSON，想不到能直接用 SQL 來處理 JSON
date: 2020-06-28 14:05:24
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://www.sqlservertutorial.net/wp-content/uploads/sql-server-tutorial.svg
---

Hi，大家好，最近玩 T-SQL 用到了很特別的 OPENJSON 函式，以往處理 JSON 都會在前後端解決，想不到 SQL 能利用 OPENJSON 函式直接把 JSON 當成一個資料表來存取

> NOTE
> 
> T-SQL OPENJSON 函式適用於 SQL Server 2016 以上

## OPENJSON 預設輸出

OPENJSON 大多會搭配 WITH 子句來使用（稍後會使用到），如果不搭配 WITH，會傳回預設輸出

1. key: 物件屬性名稱
2. value: 值
3. type: 類型
   * 0 -> null
   * 1 -> String
   * 2 -> Number
   * 3 -> Boolean
   * 4 -> Array
   * 5 -> Object
   
   類型是隨 JSON 值的型態不同而產生的數字，詳情可以看到 [官方文檔](https://docs.microsoft.com/zh-tw/sql/t-sql/functions/openjson-transact-sql?view=sql-server-ver15#return-value) 有對傳回值做詳細說明
   
### 使用 OPENJSON 剖析轉換 JSON

#### 剖析 JSON 物件

```sql=
DECLARE @json VARCHAR(MAX) = '
{
  "name": "horse",
  "age": 18,
  "isHandsome": true,
  "nullValue": null,
  "arrayValue": [1, 2, 3],
  "objectValue": { "obj": "objString" }
}
';

SELECT * FROM OPENJSON(@json);
```

![](https://i.imgur.com/6p7JUGH.png)

#### 剖析 JSON 物件陣列

```sql=
DECLARE @json VARCHAR(MAX) = '
[
  {
    "name": "horse",
     "age": 18
  },
  {
    "name": "Otis",
    "age": 18
  }
]
';

SELECT * FROM OPENJSON(@json);
```

![](https://i.imgur.com/mC2lyLd.png)

這種預設結構的顯示結果應該是或多或少會有需要用到的時候，不過筆者主要使用到的是下一種搭配 WITH 的用法，能夠帶有明確的結構，簡直就像是把 JSON 當作一張資料表來使用

## 有明確結構的 OPENJSON 輸出

使用 OPENJSON 函數的 WITH 子句指定結果的結構描述時，會傳回在 WITH 子句中所定義之資料行的資料表，逐一查看 JSON 物件陣列、讀取為每個資料行指定的值，並將值轉換成指定的型別


### 剖析物件陣列，搭配 WITH

```sql=
DECLARE @json VARCHAR(MAX) = '
[
  {
    "name": "horse",
     "age": 18
  },
  {
    "name": "Otis",
    "age": 18
  }
]
';

SELECT *
FROM OPENJSON(@json)
WITH (name  VARCHAR(100)  '$.name',
      age   INT           '$.age')
```

![](https://i.imgur.com/U2hra0B.png)

可以看到他把陣列內的兩筆物件給按照 WITH 子句所寫的結構描述來呈現

## 總結

本篇只有帶出最簡易的 OPENJSON 用法，有時 JSON 結構可能會更複雜，那要處理時所寫的 SQL 程式就不只這樣，其實還有很多操作 JSON 的函式還沒有寫到

另外，在實務上，接到一包 JSON，後續通常是要用來做 INSERT 或 UPDATE 等等的 SQL 操作

這些就留待後續的文章補上

## 參考資料

1. [使用 OPENJSON 剖析及轉換 JSON 資料 (SQL Server)](https://docs.microsoft.com/zh-tw/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server?view=sql-server-ver15)
