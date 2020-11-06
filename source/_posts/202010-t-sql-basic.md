---
title: T-SQL - 基本語法
date: 2020-10-25 23:59:44
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://cdn.worldvectorlogo.com/logos/microsoft-sql-server.svg
---

Hi，大家好，本篇紀錄 T-SQL 最基本的語法的解析

---

## 語法元素

```sql=
SELECT *
FROM Salaries
WHERE Salary >= 30000
```

### 關鍵字（Keywords）

特殊意義的保留字（Reserved Words），`SELECT`、`FROM`、`WHERE`

### 識別名稱（Identifiers）

資料庫物件的名稱

### 運算式（Expressions）

運算式取得值，如 `SELECT 欄位1 + 欄位2`

### 資料類型（Data Types）

欄位的資料類型，如 INT、VARCHAR(10)

### 函數（Functions）

有內建函數或是自訂函數，如 `GETDATE()`，自訂的是 資料表值函式 或是 純量值函式

### 註解（Comments）

```sql=
-- SELECT *
-- FROM Salaries
-- WHERE Salary >= 30000
```

---

## 指令種類

### 資料定義語言 DDL（Data Definition Language）

新增、修改、刪除資料庫物件，資料庫物件有：資料表、檢視表、索引、預存程序、函數、觸發程序等

### 資料操作語言 DML（Data Manipulation Language）

針對資料表的指令，用來新增、修改、刪除，還有查詢資料表的資料，指令：INSERT、UPDATE、DELETE、SELECT

### 資料控制語言 DCL（Data Control Language）

資料庫安全管理的權限設定，指令：GRANT、DENY、REVOKE 等等
