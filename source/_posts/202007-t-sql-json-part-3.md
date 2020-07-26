---
title: T-SQL OPENJSON - 搭配 OPENJSON 實作新增資料或修改資料
date: 2020-07-26 09:20:15
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://cdn.worldvectorlogo.com/logos/microsoft-sql-server.svg
---

Hi，大家好，最近都在大玩特玩 T-SQL，如果網頁開發的模式是前後端分離時，那要對資料庫做新增或修改，前端會把一整包 JSON 丟給後端 API，後端再想辦法用程式來解析這包 JSON ，最後組成 SQL 指令丟給 SQL 服務進行執行

這篇文章提供了一種不一樣的選擇，就是直接的把 JSON 丟給 SQL，讓 SQL 來直接處理 JSON，而後進行新增資料或修改資料

---

## 環境說明

必須是 SQL Server 2016 以上，其他資料庫就不清楚是否有類似的 JSON 函式

本篇文章主要搭配來做新增或修改的是 OPENJSON 函式，如果還不知道 OPENJSON 是什麼的讀者們，可以看看筆者之前寫的 [T-SQL OPENJSON，想不到能直接用 SQL 來處理 JSON](https://littlehorseboy.github.io/2020/06/28/2020-t-sql-json-part-1/)

### 準備一個簡單的 table

```sql=
CREATE TABLE [dbo].[Employees]
(
 Id    int            IDENTITY(1,1)  NOT NULL,
 Name  nvarchar(100)                 NOT NULL,
 CONSTRAINT [PK_Employees] PRIMARY KEY CLUSTERED (
   Id ASC
 ) WITH (
   PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON
 ) ON [PRIMARY]
) ON [PRIMARY]
```

![](https://i.imgur.com/ZXvTOPB.png)

### 還需要有一包 JSON

```JSON=
[
  {
    "id": 0,
    "name": "Otis"
  }
]
```

---

## 首先就將這包 JSON 直接 OPENJSON 起來做 INSERT

### JSON 陣列內只有一筆物件做新增時

請各位把 `@json` 當作是 API 收到的 JSON 來看

```sql=
DECLARE @json NVARCHAR(MAX) = '
[
  {
    "id": 0,
    "name": "Otis"
  }
]
';

INSERT INTO Employees (
       Name)
SELECT Name
  FROM OPENJSON(@json)
       WITH(Id    INT           '$.id',
            Name  NVARCHAR(100) '$.name')
```

執行後資料表新增了一筆資料

![](https://i.imgur.com/tBgnaC9.png)

這段程式碼可以看到 OPENJSON 就是把 JSON 當成一張資料表的概念

所以就能直接從這張資料表拉出所需的欄位來做新增的動作

### JSON 陣列內有兩筆以上物件做新增時

SQL 程式碼的 INSERT 那段沒改動，只有改變 JSON 陣列的內容

```sql=
DECLARE @json NVARCHAR(MAX) = '
[
  {
    "id": 0,
    "name": "Mary"
  },
  {
    "id": 0,
    "name": "John"
  }
]
';

INSERT INTO Employees (
       Name)
SELECT Name
  FROM OPENJSON(@json)
       WITH(Id    INT           '$.id',
            Name  NVARCHAR(100) '$.name')
```

執行後資料表新增了兩筆資料

![](https://i.imgur.com/QVhI7sY.png)

想必各位應該可以體會到，OPENJSON 能夠在每次接到 API 傳來陣列內物件數量不一的 JSON 時，進行靈活的因應並新增資料

---

## 那 UPDATE 也是一包 JSON 直接做掉嗎？

沒錯，因為 OPENJSON 能夠把 JSON 當成一張資料表，只要有給正確的 `id`，搭配 UPDATE 的 WHERE 條件，就能直接多筆資料進行 UPDATE

### 原本資料表內的資料

![](https://i.imgur.com/QVhI7sY.png)

這時筆者想要將 Mary 改成 Otis、以及 John 改成 Otis

準備的程式如下

```sql=
DECLARE @json NVARCHAR(MAX) = '
[
  {
    "id": 2,
    "name": "Otis"
  },
  {
    "id": 3,
    "name": "Otis"
  }
]
';

UPDATE a
   SET Name    = b.Name
  FROM Employees AS a,
       OPENJSON(@json)
           WITH(Id    INT           '$.id',
                Name  NVARCHAR(100) '$.name') AS b
 WHERE a.Id = b.Id
```

執行後就會對 id = 2 及 id = 3 的兩筆資料進行修改啦

![](https://i.imgur.com/VcRApa5.png)

---

## 那 DELETE 會如何呢？

把剛剛的 UPDATE SQL 指令改寫一點點就 OK 了

```sql=
DELETE a
  FROM Employees AS a,
       OPENJSON(@json)
           WITH(Id    INT           '$.id',
                Name  NVARCHAR(100) '$.name') AS b
 WHERE a.Id = b.Id
```

用什麼樣的 JSON 資料以及執行後會出現什麼結果想必讀者們應該都能在心裡推算出來了吧 XD

---

## 總結

這樣玩就會把後端寫在 API 上的邏輯，部分給轉移到 SQL 的撰寫上，像是有一大堆的 Stored Procedure XD，當然要寫在哪沒有絕對，有達成團隊共識即可
