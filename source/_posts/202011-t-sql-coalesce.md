---
title: T-SQL COALESCE，只要不是 NULL，那就來一個吧
date: 2020-11-01 22:19:56
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://www.sqlservertutorial.net/wp-content/uploads/sql-server-tutorial.svg
---

Hi，大家好，之前有寫過一篇用 NULLIF（[文章連結在此](https://littlehorseboy.github.io/2020/08/16/202008-t-sql-nullif/)） 來處理 NULL 情況的文章，這次要介紹另一種也是在 T-SQL 上處理 NULL 情況的函式，就先直接來看看範例

---

## COALESCE

```sql=
SELECT COALESCE('123', '456')
SELECT COALESCE(NULL, '123')

GO

DECLARE @null varchar(10);
SELECT COALESCE(NULL, @null)
```

執行結果

![](https://i.imgur.com/g7MY1og.png)

使用 COALESCE 函數，會依照參數的順序逐一判斷，如果遇到不為 NULL 的參數值，就會直接傳回，不管後續的參數是什麼

另一種情況是剛好全部的參數值都是 NULL，那最後還是會傳回 NULL

用 JavaScript 的陣列函式來看，還真是像是 `[].find((item) => item !== NULL)`

---

## 用 COALESCE 來替代 CASE

接下來是示範 COALESCE 與 CASE 兩者都可以達到一樣的效果的寫法

```sql=
DECLARE @a int;
DECLARE @b int;
DECLARE @c int = 5;

SELECT
CASE WHEN @a IS NOT NULL THEN @a
     WHEN @b IS NOT NULL THEN @b
     WHEN @c IS NOT NULL THEN @c ELSE NULL END AS result

SELECT
COALESCE(@a, @b, @c) AS result
```

執行結果

![](https://i.imgur.com/aZgEkoj.png)

比較後可以看到 COALESCE 的程式碼減少非常多，如果有遇到類似的多次判斷 NULL 這種需求就能派上用場了！
