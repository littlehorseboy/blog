---
title: T-SQL 旋轉 SELECT 結果，用 PIVOT 就能輕鬆達到需求
date: 2020-05-31 22:46:53
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://www.sqlservertutorial.net/wp-content/uploads/sql-server-tutorial.svg
---

Hi，大家好，最近在工作上玩 T-SQL，正巧遇到需要將一列一列的資料

像這樣

![](https://i.imgur.com/GbM5MS4.png)

給旋轉成這種一列的結果

![](https://i.imgur.com/Zb7hRic.png)

正巧得知 PIVOT 可以輕鬆達到這種需求，成功解決後就想特別寫這篇來記錄一下 PIVOT 的使用心得

## 建立 #TempTable 的示範資料

在 Microsoft Docs 的文檔中，`CREATE TABLE #table` 這個技巧稱為 [暫存資料表](https://docs.microsoft.com/zh-tw/sql/t-sql/statements/create-table-transact-sql?view=sql-server-ver15#temporary-tables)，詳細可以點連結進去看看，文檔超詳細說明了暫存資料表的細節

這邊寫的 SQL 程式碼就是利用 `CREATE TABLE #table` 來建立暫存資料表，並且在執行完動作馬上 `DROP TABLE`，如果沒有 DROP，這個 #table 則會在中斷連接時清除，在這裡是為了讓每次執行 SQL 都有一個乾淨 table 來呈現，所以都有用 DROP

```sql=
CREATE TABLE #table (
	No INT IDENTITY(1, 1) PRIMARY KEY,
	Name VARCHAR(10),
	Value VARCHAR(10)
)

INSERT INTO #table (Name, Value)
	VALUES ('1001', '32'), ('1002', '14'), ('1003', '48.3'), ('1004', '7'), ('1005', '46')
    
----------

SELECT * FROM #table

----------

DROP TABLE #table
```

本次要拿來玩的資料就長這樣

![](https://i.imgur.com/GbM5MS4.png)

---

## 利用 PIVOT 來旋轉結果

在資料中，欄位 No 只是用來當 primary key 的流水號，所以這邊沒使用到 No

用 PIVOT 是想要讓 `欄位 Name 的值` 可以 `成為欄位名稱`，`欄位 Value 的值` 對應使用了哪些 Name，就會將值帶入旋轉後的結果

```sql=
CREATE TABLE #table (
	No INT IDENTITY(1, 1) PRIMARY KEY,
	Name VARCHAR(10),
	Value VARCHAR(10)
)

INSERT INTO #table (Name, Value)
	VALUES ('1001', '32'), ('1002', '14'), ('1003', '48.3'), ('1004', '7'), ('1005', '46')
    
----------

SELECT  [1001], [1002], [1003], [1004], [1005] FROM (
	SELECT Name, Value
	FROM #table
) AS SourceTable
PIVOT
(
	MAX(Value)
	FOR Name IN ([1001], [1002], [1003], [1004], [1005])
) AS PivotTable

----------

DROP TABLE #table
```

> 要注意到的是 PIVOT 勢必要搭配一個彙總函式，例如 SUM、COUNT 或這邊用了 MAX
> 
> SUM 或是 COUNT 這些勢必要是 `數值` 內容才能使用
> 
> 不過 MAX 可以用在 VARCHAR NVACHAR 之類的欄位值

結果如下，輕輕鬆鬆就能達成旋轉資料囉

![](https://i.imgur.com/Zb7hRic.png)

---

## 特別加映，動態 PIVOT，使用 `EXEC sp_executesql`

因為需求變更，不是每次都需要拿 [1001], [1002], [1003], [1004], [1005] 這五個 Name 的值來做旋轉查詢，有時可能只要 [1001], [1003] 兩個 Name 的值，亦或是 Name 的值其實有十種，那寫 PIVOT 就會因為 `FOR Name IN ([1001], [1002], [1003], [1004], [1005])` 必須要指定需要的值，而有所限制，改動起來就些許麻煩

想必各位在寫後端就有常常利用後端程式碼來組 SQL 字串，現在筆者就是要利用組字串的方式，來讓 PIVOT 可以突破 FOR IN () 的限制

程式碼中宣告一個 @PivotSQL 放 SQL 字串，@ColumnGroup 則是需要旋轉的 Name 的值

```sql=
CREATE TABLE #table (
	No INT IDENTITY(1, 1) PRIMARY KEY,
	Name VARCHAR(10),
	Value VARCHAR(10)
)

INSERT INTO #table (Name, Value)
	VALUES ('1001', '32'), ('1002', '14'), ('1003', '48.3'), ('1004', '7'), ('1005', '46')
    
----------

DECLARE @ColumnGroup NVARCHAR(200) = '[1001], [1002], [1003], [1006]'
DECLARE @PivotSQL NVARCHAR(MAX)

SET @PivotSQL = N'
	SELECT  ' + @ColumnGroup + N' FROM (
		SELECT Name, Value
		FROM #table
	) AS SourceTable
	PIVOT
	(
		MAX(Value)
		FOR Name IN ( ' + @ColumnGroup + N')
	) AS PivotTable
'

EXEC sp_executesql @PivotSQL

----------

DROP TABLE #table
```

[1006] 因為 Name 沒有這個值，會回 NULL 給你

![](https://i.imgur.com/QvOaGfs.png)

可以看到只要改變 @ColumnGroup，就能達到動態需求的旋轉結果，那麼就將這包寫成 SP 或是資料表值函式，用傳進參數的方式就 OK 了

## 總結

在這篇文中用到 #TempTable 暫存資料表、PIVOT 旋轉查詢結果 以及 EXEC sp_executesql 執行 SQL 字串，這些技巧再搭配上 SP 或是函數撰寫都是相當有幫助的

其實使用 PIVOT 的情境，筆者認為最大宗的應該是統計分析查詢，像是月報表，年報表之類的，用 SUM 或是 AVG 搭配 FOR IN ([月份])，來取得一年之中的每月總合或每月平均這種查詢
