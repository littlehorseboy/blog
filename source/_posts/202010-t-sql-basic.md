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

## 指令種類

### DDL

### DML

### DCL
