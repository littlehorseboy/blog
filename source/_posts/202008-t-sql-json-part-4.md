---
title: T-SQL MERGE - 一次決定 OPENJSON 的資料要新增或是修改
date: 2020-08-08 20:36:46
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://www.sqlservertutorial.net/wp-content/uploads/sql-server-tutorial.svg
---

Hi，大家好，在前篇文 [T-SQL OPENJSON - 搭配 OPENJSON 實作新增資料或修改資料](https://littlehorseboy.github.io/2020/07/26/202007-t-sql-json-part-3/) 中，使用了 INSERT 或 UPDATE，個別的比對 OPENJSON 轉換的 JSON 來分別進行新增或修改

在本篇要利用 MERGE 將上述的個別比對，然後個別進行新增或修改的這些程式給合成一句

---

## 環境說明

MERGE 在所有的 SQL Server 版本都有支援，代表很早就已經存在了 MERGE 這個用法，感覺是一種特別而且不常見的用法（也可能只是筆者的歷程剛好都沒用過 XD）

在現今網路傳輸都是用 JSON 溝通的時代，用 OPENJSON 來解析 JSON，而後再搭配上 MERGE，似乎又擦出了很不一樣的火花

### 準備一個示範用的簡單 table，新增幾筆資料進去，後續 UPDATE 時會用到

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

INSERT INTO Employees([Name])
VALUES ('John'),
       ('Mary')
```

### SELECT 結果

![](https://i.imgur.com/yVqxtbx.png)

### 還需要有一包 JSON，內容大概是長這樣

```JSON=
[
  {
    "id": 0,
    "name": "Otis"
  },
  {
    "id": 2,
    "name": "Alice"
  }
]
```

---

## MERGE

MERGE 是利用資料表互相比對，接著按照比對的結果，如果是 `WHEN MATCHED`，就進行 UPDATE，如果是 `WHEN NOT MATCHED`，就進行 INSERT，這樣就能用來一次執行就決定一包 JSON 的內容是要對資料表進行新增或是修改的動作

再來看看原本的寫法

### 搭配 OPENJSON 的個別 INSERT 或 UPDATE

```sql=
INSERT INTO Employees(Name)
SELECT Name
  FROM OPENJSON(@json)
       WITH(Id    INT           '$.id',
            Name  NVARCHAR(100) '$.name')
            
GO

UPDATE a
   SET Name = b.Name
  FROM Employees AS a,
       OPENJSON(@json)
           WITH(Id    INT           '$.id',
                Name  NVARCHAR(100) '$.name') AS b
 WHERE a.Id = b.Id
```

這兩句可以合成一句

### 改寫成 MERGE INTO

```sql=
DECLARE @json NVARCHAR(MAX) = '
[
  {
    "id": 0,
    "name": "Otis"
  },
  {
    "id": 2,
    "name": "Alice"
  }
]
';

 MERGE INTO Employees AS target
 USING (SELECT *
          FROM OPENJSON(@json)
               WITH(Id    INT           '$.id',
                    Name  NVARCHAR(100) '$.name'))
            AS source(Id, Name)
    ON (target.Id = source.Id)

  WHEN MATCHED THEN
UPDATE SET target.Name = source.Name

  WHEN NOT MATCHED THEN
INSERT(Name) VALUES(Name);
```

### 執行後查詢結果

![](https://i.imgur.com/ebqdyq1.png)

可以發現到

```javascript
{
  "id": 0,
  "name": "Otis"
}
```

JSON 的這一項物件，因為 Id 沒有對應到，所以新增了一筆新的資料

```javascript
{
  "id": 2,
  "name": "Alice"
}
```

Alice 這一項則是 Id = 2 有資料符合，就做了修改的動作，將 Mary 改成了 Alice

## 程式碼逐行說明

**第 14 ~ 20 行** MERGE，USING，ON

```sql
 MERGE INTO Employees AS target
 USING (SELECT *
          FROM OPENJSON(@json)
               WITH(Id    INT           '$.id',
                    Name  NVARCHAR(100) '$.name'))
            AS source(Id, Name)
    ON (target.Id = source.Id)
```

`MERGE` 及 `USING` 分別對應一張資料表，要新增資料的資料表是 MERGE INTO 後的 target，用來比對的資料表為 USING 後的 source，`ON (target.Id = source.Id)` 是比對兩張表的關鍵

**第 22 ~ 23 行** WHEN MATCHED

```sql
  WHEN MATCHED THEN
UPDATE SET target.Name = source.Name
```

`ON (target.Id = source.Id)` 比對兩張表如果 target 與 source 有對應到的資料，就會進行 UPDATE

**第 25 ~ 26 行** WHEN NOT MATCHED

```sql
  WHEN NOT MATCHED THEN
INSERT(Name) VALUES(Name);
```

`ON (target.Id = source.Id)` 比對兩張表如果 target 沒有對應到 source 的資料，就可以直接將 source 的資料 INSERT 到 target 的資料表之中
