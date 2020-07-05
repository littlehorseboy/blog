---
title: T-SQL TRANSACTION - 巢狀交易原來是要這樣思考的阿！？
date: 2020-07-05 23:01:16
categories:
- w3HexSchool
- T-SQL
tags:
- w3HexSchool
- T-SQL
cover: https://cdn.worldvectorlogo.com/logos/microsoft-sql-server.svg
---

Hi，大家好，最近在玩 T-SQL，如果是要讓一次執行中的多種的 INSERT、UPDATE、DELETE 語法能在其中任何一種發生錯誤時，就全部退回去，將資料回復成原樣，這時就可以用交易（TRANSACTION）的功能，讓執行的語法全部成功會正常寫入或一有失敗就沒改變任何資料，TRANSACTION 可以滿足 ACID 的規範（**不可分割性**、**一致性**、**隔離性**、**持久性**）

這次是用 T-SQL 寫了巢狀交易的程式，一支 SP 來執行另外兩支 SP，三支 SP 的程式碼都有用到 TRAN，結果丟資料執行後雖然看起來有正常執行，但如果故意弄錯誤資料來讓它跑 ROLLBACK，卻是報錯說 `找不到該名稱的交易或儲存點`，一開始對這個錯誤訊息真的摸不著頭緒，卡了好一陣子，甚至一度誤認為巢狀交易是不是無法做到的

但就在某個寂靜的夜晚，Google 搜尋了 「巢狀交易」，才終於發現真理以及原來是還有額外的語法要使用，才能讓 `找不到該名稱的交易或儲存點` 這個錯誤訊息消失

接下來會先示範簡單的 TRANSACTION 範例，而後示範筆者原本寫到會報錯的巢狀交易寫法，最後就是正確的巢狀交易寫法，因為希望能在文中簡易呈現，所以不是用 SP 來示範

---

## 簡單的 TRANSACTION 範例

### 建立一張 Table 來 INSERT

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

寫兩段 INSERT 指令來執行一次

```sql=
INSERT INTO [dbo].[Employees] ([Name])
VALUES ('Otis') -- 這筆能正常 INSERT

INSERT INTO [dbo].[Employees] ([Name])
VALUES (NULL) -- 這筆因為不能 NULL 而失敗
```

執行結果

![](https://i.imgur.com/l1Ya3eN.png)

SELECT 結果就是有成功寫入一筆

![](https://i.imgur.com/QgR8Eqj.png)

### 加上 TRY CATCH 及 TRANSACTION

先執行了這段讓 TABLE 回到沒資料的狀態，IDENTITY 也會重新開始編號

```sql
TRUNCATE TABLE Employees -- 移除 TABLE Employees 的所有資料列
```

現在筆者希望這兩段語法，如果有其中一筆失敗了，就不能夠寫入到資料庫

```sql=
BEGIN TRY
      BEGIN TRAN; -- 啟動交易

      INSERT INTO [dbo].[Employees] ([Name])
      VALUES ('Otis');

      INSERT INTO [dbo].[Employees] ([Name])
      VALUES (NULL); -- 這筆因為不能 NULL 而失敗，會進到 CATCH 區塊進行 ROLLBACK

      COMMIT TRAN; -- 交易成功
END TRY
BEGIN CATCH
      ROLLBACK TRAN; -- 回復到交易的開頭

      THROW; -- 拋出錯誤訊息
END CATCH
```

執行結果

![](https://i.imgur.com/FA5gIbq.png)

> Note:
> 如果沒有寫 THROW，就不會顯示紅色字那段錯誤訊息

SELECT 結果就是沒資料被寫入，原本能夠成功寫入的那段語法，被 ROLLBACK 給回復了

![](https://i.imgur.com/2uJusvB.png)

如此這個簡單的 TRANSACTION 範例就完成了

> Note:
> Rollback 回復交易後，雖然執行的 INSERT 都會被回復掉，但是 IDENTITY 不會被重置，也就是 Id 這個欄位因為 INSERT 過兩筆而跳了兩號
> 
> 讀者們可以試試看再 INSERT 成功的指令，就能夠觀察到 IDENTITY 是從 3 開始

---

## 巢狀交易，錯誤的寫法

原本以為交易包交易很單純，結構有正確應該就會乖乖的按照區塊 ROLLBACK 才是

### 錯誤的寫法 - 1

```sql=
BEGIN TRY
      BEGIN TRAN;

      -- innerTran
      BEGIN TRY
            BEGIN TRAN;

            INSERT INTO [dbo].[Employees] ([Name])
            VALUES ('Otis');

            INSERT INTO [dbo].[Employees] ([Name])
            VALUES (NULL);

            COMMIT TRAN;
      END TRY
      BEGIN CATCH
            ROLLBACK TRAN;

            THROW;
      END CATCH
      -- end innerTran

      COMMIT TRAN;
END TRY
BEGIN CATCH
      ROLLBACK TRAN;

      THROW;
END CATCH
```

執行結果

![](https://i.imgur.com/j9rlQQ1.png)

內心 OS

筆者：沒有對應的 BEGIN TRANSACTION！？

筆者：那是不是我只要把每一個 TRAN 給它們名字呢，它們就會各自對應了呢？

筆者：就加吧加吧

---

### 錯誤的寫法 - 2

```sql=
BEGIN TRY
      BEGIN TRAN outterTran;

      -- innerTran
      BEGIN TRY
            BEGIN TRAN innerTran;

            INSERT INTO [dbo].[Employees] ([Name])
            VALUES ('Otis');

            INSERT INTO [dbo].[Employees] ([Name])
            VALUES (NULL);

            COMMIT TRAN innerTran;
      END TRY
      BEGIN CATCH
            ROLLBACK TRAN innerTran;

            THROW;
      END CATCH
      -- end innerTran

      COMMIT TRAN outterTran;
END TRY
BEGIN CATCH
      ROLLBACK TRAN outterTran;

      THROW;
END CATCH
```

執行結果

![](https://i.imgur.com/8hbjrHd.png)

內心 OS

筆者：What！？

筆者：怎麼反而是很明確的告訴了我 innerTran 無法回復，程式不就整齊的 BEGIN + END 嗎，到底是什麼問題ㄚㄚㄚ？？？

筆者：而且還少了應該出現的無法插入 NULL 的錯誤訊息，但是的確都還是有執行 ROLLBACK，資料表都沒有被寫入資料

筆者：（左思右想，胡思亂想）

這邊開始就卡了好一段時間，甚至是放棄了巢狀寫法，畢竟有錯誤訊息感覺就很不穩，把內層的 TRAN 給拿掉了

---

## 巢狀交易，正確的寫法

後來因為查到一篇文章寫到這句（文章連結附在文末的參考資料，超級推薦）

> Note:
> 巢狀交易時，ROLLBACK 不管在哪一層，它會 ROLLBACK 巢狀中的所有交易

這一句猶如醍醐灌頂，原來無法回復，找不到交易或儲存點就是因為這個原因！！

另外還有在文中看到 SAVE TRANSACTION 這個在交易內設定儲存點的指令，可以用來回復局部 ROLLBACK

查了一下官方文檔，還直接有 [程式碼範例](https://docs.microsoft.com/zh-tw/sql/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15)，拿來邊參考邊改程式

### 正確的寫法

```sql=
BEGIN TRY
      BEGIN TRAN outterTran;

      -- innerTran
      DECLARE @TranCounter INT = @@TRANCOUNT;

      BEGIN TRY
            -- @TranCounter > 0 代表目前已經是在交易內，改用 SAVE TRAN
            IF (@TranCounter > 0) SAVE TRAN innerTran;
            ELSE BEGIN TRAN innerTran;

            INSERT INTO [dbo].[Employees] ([Name])
            VALUES ('Otis');

            INSERT INTO [dbo].[Employees] ([Name])
            VALUES (NULL);

            -- SAVE TRAN 不做 COMMIT
            IF (@TranCounter = 0) COMMIT TRAN innerTran;
      END TRY
      BEGIN CATCH
            -- SAVE TRAN 局部交易回復
            ROLLBACK TRAN innerTran;

            THROW;
      END CATCH
      -- end innerTran

      COMMIT TRAN outterTran;
END TRY
BEGIN CATCH
      ROLLBACK TRAN outterTran;

      THROW;
END CATCH
```

> Note:
> @@TRANCOUNT = 0 代表是最底層的 TRANSACTION
> 
> @@TRANCOUNT > 0 則是巢狀內的 TRANSACTION

執行結果

![](https://i.imgur.com/TvHHZEa.png)

終於沒有那種找不到交易的錯誤訊息了 XD！！可喜可賀

## 總結

「巢狀交易」這四個字是在 [官方文件的 BEGIN TRANSACTION](https://docs.microsoft.com/zh-tw/sql/t-sql/language-elements/begin-transaction-transact-sql?view=sql-server-ver15#general-remarks) 看到的

有一段時間是這樣子的歷程，雖然看的是官方文件，但因為 T-SQL 的經驗不夠，反而誤解了文件所表達的東西，以為巢狀交易是無法達成的 XD

後來就是反覆的實驗，寫 CODE 執行，爬更多文，再重新讀文檔，這樣的循環，才終於弄清楚那段錯誤訊息的含意，真的花了不少時間 XD，也就這麼催生了想寫這篇文的想法

## 參考資料

https://docs.microsoft.com/zh-tw/sql/t-sql/language-elements/begin-transaction-transact-sql?view=sql-server-ver15#general-remarks

https://docs.microsoft.com/zh-tw/sql/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15

大推這位大大的 Understanding Transaction，它就是在那個寂靜的夜晚，Google 搜尋「巢狀交易」四個字查到，然後直接打通我的任督二脈的超厲害文章
http://vito-note.blogspot.com/2013/05/transactions.html
