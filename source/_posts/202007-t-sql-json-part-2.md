---
title: T-SQL JSON 函式，想不到能直接用 SQL 來處理 JSON
date: 2020-07-19 12:40:34
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://cdn.worldvectorlogo.com/logos/microsoft-sql-server.svg
---

Hi，大家好，前陣子寫了一篇 T-SQL 使用 OPENJSON 函式，可以將 JSON 當成資料表來使用的文章（[T-SQL OPENJSON，想不到能直接用 SQL 來處理 JSON](https://littlehorseboy.github.io/2020/06/28/2020-t-sql-json-part-1/)），這次要介紹一些也是處理 JSON 的 T-SQL 函式，可以用來直接擷取 JSON 的某個 property 的值，或是變更 property 的值

---

## JSON 函式

以下就是本次文章所要實際用範例介紹並示範的 JSON 函式

| 函式         | 描述         |
| ----------- | ----------- |
| JSON_VALUE  | 從 JSON 字串擷取值 |
| JSON_QUERY  | 從 JSON 字串擷取 Object 或 Array |
| JSON_MODIFY | 變更 JSON 字串的屬性值 |

---

## JSON_VALUE

從 JSON 字串擷取某 property 的純量值，傳回型別為 NVARCHAR(4000)

> Note:
> JSON_VALUE 的傳回值型態是 NVARCHAR(4000)，如果擷取的值大於 4000 個字的話，會回傳 NULL，如果沒有讀到文檔這段說明，可能會因為不小心抓的 property 超過字數，但卻沒有看到任何錯誤訊息而感到困惑（有被雷過 XD）

## JSON_VALUE 擷取純量值

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

SELECT JSON_VALUE(@json, '$.name')            AS Name,
       JSON_VALUE(@json, '$.age')             AS Age,
       JSON_VALUE(@json, '$.isHandsome')      AS IsHandsome,
       JSON_VALUE(@json, '$.nullValue')       AS NullValue,
       JSON_VALUE(@json, '$.arrayValue')      AS ArrayValue,
       JSON_VALUE(@json, '$.objectValue')     AS ObjectValue,
       JSON_VALUE(@json, '$.objectValue.obj') AS Obj
```

## SELECT 結果

![](https://i.imgur.com/bXFcexF.png)

arrayValue 及 objectValue 的值分別是 `[1, 2, 3]`、`{ "obj": "objString" }`

因為是 JSON，所以用 JSON_VALUE 取會得到 NULL

如果想要取得 JSON 得使用 JSON_QUERY 才是正確的方式

---

## JSON_QUERY

從 JSON 字串擷取物件或陣列，傳回型別為 NVARCHAR(MAX)

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

SELECT JSON_VALUE(@json, '$.name')            AS Name,
       JSON_VALUE(@json, '$.age')             AS Age,
       JSON_VALUE(@json, '$.isHandsome')      AS IsHandsome,
       JSON_VALUE(@json, '$.nullValue')       AS NullValue,
       JSON_QUERY(@json, '$.arrayValue')      AS ArrayValue,
       JSON_QUERY(@json, '$.objectValue')     AS ObjectValue,
       JSON_VALUE(@json, '$.objectValue.obj') AS Obj
```

這裡把擷取 arrayValue 及 objectValue 的函式改成用 JSON_QUERY 取值，就能得到 JSON 物件或陣列

## SELECT 結果

![](https://i.imgur.com/proYxVr.png)

---

## JSON_MODIFY

更新 JSON 字串中的 property 的值，並傳回更新後的 JSON 字串

```sql=
DECLARE @json VARCHAR(MAX) = '
{
  "name": "horse",
  "arrayValue": [1, 2, 3],
  "objectValue": { "obj": "objString" }
}
';

PRINT JSON_MODIFY(@json, '$.name', 'Otis');

PRINT JSON_MODIFY(@json, '$.arrayValue[0]', 0);

PRINT JSON_MODIFY(@json, '$.objectValue.obj', 'newValue');
```

每一列 PRINT 的呈現結果如下

## JSON_MODIFY 改 name 的 PRINT 結果

```sql=
PRINT JSON_MODIFY(@json, '$.name', 'Otis');
```

<img src="https://i.imgur.com/89axFpp.png" style="border: 1px solid #ccc">

## JSON_MODIFY 改 arrayValue[0] 的 PRINT 結果

```sql=
PRINT JSON_MODIFY(@json, '$.arrayValue[0]', 0);
```

<img src="https://i.imgur.com/lPaT1ic.png" style="border: 1px solid #ccc">

## JSON_MODIFY 改 objectValue.obj 的 PRINT 結果

```sql=
PRINT JSON_MODIFY(@json, '$.objectValue.obj', 'newValue');
```

<img src="https://i.imgur.com/PW4tyYl.png" style="border: 1px solid #ccc">

## 如果要保留每次的改動，得 SET 回變數

前幾個使用 JSON_MODIFY 後可以發現到使用該函式是會回傳新的物件

需要用 `SET @json` 的方式來讓變數紀錄住回傳的新物件

```sql=
DECLARE @json VARCHAR(MAX) = '
{
  "name": "horse",
  "arrayValue": [1, 2, 3],
  "objectValue": { "obj": "objString" }
}
';

SET @json = JSON_MODIFY(@json, '$.name', 'Otis');

SET @json = JSON_MODIFY(@json, '$.arrayValue[0]', 0);

SET @json = JSON_MODIFY(@json, '$.objectValue.obj', 'newValue');

PRINT @json;
```

## PRINT @json 結果

<img src="https://i.imgur.com/T3eBhQK.png" style="border: 1px solid #ccc">

---

## 總結

以上就是 JSON_VALUE、JSON_QUERY、JSON_MODIFY 的簡單示範

下一篇預計會講到 MEAGE INTO 搭配 OPENJSON 是如何直截了當的用 JSON 來做 INSERT 或 UPDATE
